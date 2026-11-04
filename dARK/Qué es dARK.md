# Qué es dARK

**dARK** (*Decentralized Archival Resource Key*) es la plataforma de LA Referencia para asignar identificadores ARK de forma descentralizada. Un repositorio externo (en nuestro caso OJS) deposita un objeto con sus metadatos a través de la API. dARK le asigna un ARK bajo la autoridad dueña del NAAN, guarda los metadatos en un **IPFS privado**, publica el registro en una **blockchain privada** (Besu/QBFT) y lo expone para su resolución por HTTP.

> Sobre en qué se diferencia un dARK de un ARK, ver [[Notas Lauti]]: shoulder con DNA, SNA y delimitador numérico.

## Capas

```text
SERVICIOS     Admin API · Minter API · Resolver API · Store API · Dashboard
              │  dark-core-lib (SDK en Python: DARKCoreClient)
BLOCKCHAIN    Authority.sol (control de acceso) ← IAuthority.sol → dARK.sol (registro de ARK)
              │  red privada Besu/QBFT, chain ID 2025
ALMACENAMIENTO  Kubo + IPFS Cluster · Store API
```

| Componente | Rol | Puerto |
|---|---|---|
| `dark-core-admin-api` | Administración de autoridades y NAAN | 8000 |
| `dark-core-minter-api` | Reserva, metadatos, publicación y workers. **Es con quien habla el plugin** | 8001 |
| `dark-core-resolver-api` | Resolución pública (redirección, `?info`, `?metadata`) | 8002 |
| `dark-store-api` | Fachada sobre IPFS | 8003 |
| `dark-dapp` | Contratos Solidity | — |
| `dark-core-lib` | SDK en Python compartido. **Público**; de ahí sale la firma Ed25519 | — |

## Modelo de autoridades
- `Authority.sol` vincula **una wallet con un UUID de autoridad** y controla qué NAAN puede escribir cada autoridad.
- La autoridad es lo que configuramos como «UUID de la revista» en el plugin. Ver [[Configuración, asignación y exposición]].
- Registrar autoridades, autorizar NAAN y registrar claves de cliente es tarea del **administrador** (Admin API).

## Flujo de publicación
`reservar → completar metadatos → persistir → publicar → resolver`

1. **Reserva** (`POST /arks`): se genera el ARK en estado **R**.
2. **Metadatos** (`PUT /arks/{ark}`): pasa a **D** (draft).
3. El *metadata worker* guarda L1 y L2 y obtiene sus CID; el *replication worker* confirma el pin en IPFS.
4. El *chain worker* publica en la blockchain: estado **P**.
5. Un PUT posterior sobre un ARK publicado lo pasa a **U** hasta que se mina la actualización.

El registro en el minter es **sincrónico** (PostgreSQL) y devuelve el control enseguida. La persistencia en IPFS y la transacción on-chain son **asíncronas**, por eso el plugin necesita consultar el estado periódicamente.

## Estados de un ARK

| Estado | Código | Significado |
|---|---|---|
| reserved | R | ID generado, sin metadatos |
| draft | D | Metadatos recibidos, pendiente de IPFS y de la blockchain |
| update | U | Actualización pendiente on-chain |
| published | P | Vivo en la blockchain |
| tombstone | T | Retirado, no se puede modificar. **Hoy solo en la base del minter** (ver [[Pendientes y dudas abiertas#8. Ciclo de vida]]) |

Mapeo con los estados de `ArkRecord` en el plugin: **pendiente**. Ver [[Deuda técnica]].

## Resolver
- Público y sin autenticación.
- `GET /api/v1/arks/{ark}` redirige al `target` (302 por defecto).
- `?info` devuelve la información pública proyectada desde L1; `?metadata` muestra la vista de metadatos.
- Falta definir la URL pública definitiva. Ver [[Pendientes y dudas abiertas#2. Prefijo, shoulder y resolver]].

Siguiente: [[API del minter]]
