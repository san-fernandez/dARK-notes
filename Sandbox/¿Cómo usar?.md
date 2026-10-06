El sandbox es un entorno Docker para desarrollar y probar el plugin dARK sobre OJS 3.3, 3.4 y 3.5 con una base MySQL 8 compartida y montaje en caliente.

---
## Estructura del Sandbox

El sandbox y el plugin conviven como directorios hermanos dentro del mismo workspace:

```text
workspace/
├── dark-pubid-plugin/      Repositorio con el código del plugin dARK
├── dark-worktrees/         Checkouts vinculados creados automáticamente por make init
│   ├── ojs33/              Directorio vinculado a la rama dev-3_3
│   ├── ojs34/              Directorio vinculado a la rama dev-3_4
│   └── ojs35/              Directorio vinculado a la rama dev-3_5
└── ojs-sandbox/            Este repositorio con la infraestructura
    ├── fixtures/configs/   Archivos config.inc.php de cada versión
    ├── fixtures/files/     Directorio persistente de archivos subidos
    ├── fixtures/sql/       Dumps SQL de inicialización automática
    ├── images/             Dockerfiles de cada versión de OJS
    └── scripts/            setup-worktrees.sh y snapshot.sh
```


---
## Servicios y Accesos

Todos los entornos vienen preconfigurados con usuario **admin** y clave **admin**:

- OJS 3.3 (PHP 7.3): [http://localhost:8033](http://localhost:8033) (Base de datos: `ojs_33`)
    
- OJS 3.4 (PHP 8.0): [http://localhost:8034](http://localhost:8034) (Base de datos: `ojs_34`)
    
- OJS 3.5 (PHP 8.2): [http://localhost:8035](http://localhost:8035) (Base de datos: `ojs_35`)
    
- phpMyAdmin: [http://localhost:8080](http://localhost:8080) (login: `root/root` u `ojs/ojs`)
    
- MySQL 8.0: `localhost:3306` (usuario: `ojs`, clave: `ojs`)
    


---
## Comandos Básicos

Se controlan desde la raíz de `ojs-sandbox` mediante `make`:

- `make init`: Da permisos a scripts, crea carpetas y vincula los worktrees.
    
- `make up-all`: Levanta MySQL, phpMyAdmin y las tres versiones de OJS.
    
- `make up-33`: Levanta solo OJS 3.3, MySQL y phpMyAdmin (ahorra RAM y CPU).
    
- `make up-34`: Levanta solo OJS 3.4, MySQL y phpMyAdmin.
    
- `make up-35`: Levanta solo OJS 3.5, MySQL y phpMyAdmin.
    
- `make down`: Detiene todos los contenedores sin borrar los datos.
    
- `make clean`: Borra los contenedores y el volumen de datos para volver al estado inicial de los seeds.
    
- `make snapshot-33`, `make snapshot-34`, `make snapshot-35`: Guarda el estado actual de la base de datos en `fixtures/sql/` para versionarlo en Git.

---
## Worktrees y Flujo de Desarrollo

### ¿Qué son los worktrees?

Un **git worktree** permite tener múltiples ramas de un mismo repositorio Git abiertas simultáneamente en distintas carpetas del disco, sin necesidad de cambiar constantemente de rama con `git checkout`.

En este proyecto:

- `dark-worktrees/ojs33` contiene el checkout de la rama `dev-3_3`.
    
- `dark-worktrees/ojs34` contiene el checkout de la rama `dev-3_4`.
    
- `dark-worktrees/ojs35` contiene el checkout de la rama `dev-3_5`.
    

### ¿Cómo se conectan al Sandbox?

Cada contenedor de OJS monta en caliente su carpeta correspondiente directamente en la ruta del plugin:

```text
/var/www/html/plugins/pubIds/dARK
```

### ¿Cómo usarlos para desarrollar?

#### 1. Inicializar los worktrees desde `ojs-sandbox`

```bash
cd ../ojs-sandbox
make init
```

#### 2. Levantar la versión en la que vas a trabajar

```bash
make up-33
# o make up-34 / make up-35
```

#### 3. Editar el código

Abrí en tu editor la carpeta del worktree que necesites (ejemplo, `dark-worktrees/ojs33`). Cualquier cambio que guardes en los archivos impacta de forma inmediata dentro de OJS sin tener que reiniciar contenedores ni recompilar imágenes. Se pueden crear ramas nuevas de desarrollo y luego pushearlas en los repositorios.
**Aclaración:** cada vez que se haga `make init` se volverán a poner los plugins en sus ramas de desarrollo.

#### 4. Limpiar caché si OJS no refleja cambios en plantillas o traducciones

```bash
docker compose exec ojs33 rm -rf \
  /var/www/html/cache/*.php \
  /var/www/html/cache/t_compile/*
```

#### 5. Ver logs y errores PHP en vivo

```bash
docker compose logs -f ojs33
```