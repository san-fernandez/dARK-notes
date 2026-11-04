# Importación por CSV (Entrega 2)

Estado: hecha en `dev-3_3`, `dev-3_4` y `dev-3_5` (PR #34 del 30/10, más arreglos hasta el 02/11). **Falta probar con un CSV real y etiquetar** (E2-T5).

Ruta: Herramientas → Importar/Exportar → *Módulo de importación de identificadores dARK (CSV)*.

## Flujo
1. **Subir** el archivo (`uploadImportCSV`). Se valida la extensión y el tamaño.
2. **Reporte previo** (`import`): el core analiza cada fila. **No se modifica nada** hasta confirmar.
3. **Confirmar** (`confirmImport`): se guardan **solo las filas válidas**, y cada una queda con su resultado.
4. **Descargar el reporte** (`downloadReport`) en CSV, con las columnas `fila`, `oai`, `ark`, `resultado_importacion` y `mensaje` (traducibles).

## Formato aceptado hoy

| Aspecto | Valor |
|---|---|
| Codificación | UTF-8, con o sin BOM |
| Separador | `,` o `;` |
| Cabecera | Obligatoria: `oai,ark` |
| Límites | 5 MB y 2000 filas |
| Extensión | `.csv` |

```csv
oai,ark
oai:ojs34.localhost:article/2,ark:/12345/art2-csv
```

## Validaciones por fila (`CsvRecord`)

| Código | Caso |
|---|---|
| `ARK_IS_EMPTY` / `OAI_IS_EMPTY` | Falta un valor |
| `INVALID_ARK_FORMAT` | El ARK no tiene un formato válido |
| `ARK_PREFIX_NOT_MATCH` | El prefijo no es el de la revista |
| `ARK_IS_DUPLICATED_IN_FILE` | El ARK aparece más de una vez en el archivo |
| `OAI_IS_DUPLICATED_IN_FILE` | El OAI aparece más de una vez en el archivo |
| `OAI_NOT_EXISTS` | No hay un artículo con ese OAI en la revista |
| `ARTICLE_ALREADY_HAS_ARK` | El artículo ya tiene un ARK; la fila se omite, no se sobrescribe |
| `ARK_IS_TAKEN` | El ARK ya lo tiene otro artículo |

Errores de archivo (`ParserError`): `invalid_header`, `invalid_encoding`, `invalid_separator`, `empty_file`, `no_data_rows`, `invalid_extension`, `file_too_large` y `too_many_rows`.

**Vinculación:** el OAI se traduce a `submissionId` con `oai:<repository_id>:article/<id>` (`ArticleProvider`), en bloques de 500.

Archivos de prueba en `test-data/`: `valid.csv`, `semicolon_bom.csv`, `invalid_header.csv`, `invalid_encoding.csv`, `empty.csv`, `header_only.csv`, `ark_taken.csv`, `all_errors.csv` y `not_a_csv.txt`.

## Diferencias con la especificación
- La especificación proponía las columnas `oai_identifier`, `target` y `ark`, con `ark` obligatorio y al menos uno de los otros dos. **El plugin usa `oai` y `ark`, las dos obligatorias.**
- La especificación mencionaba la opción **sobrescribir**; no está implementada.
- La herramienta del minter usa `darkidentifier` e `itemurl` y vincula por la URL.

Todo esto está abierto en [[Pendientes y dudas abiertas#5. Importación por CSV]].
