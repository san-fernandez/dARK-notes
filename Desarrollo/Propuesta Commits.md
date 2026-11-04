# Convención de commits, ramas y PRs

## Mensajes de commit

`TIPO : DESCRIPCIÓN`

También vale con alcance opcional: `tipo(alcance): descripción`, por ejemplo `fix(importexport): ...` o `fix(ojs34): ...`.

Tipos de commits:
+ feat : funcionalidad nueva
+ fix : corrección de un bug
+ refactor : reestructuración sin cambio de comportamiento
+ docs : solo documentación
+ chore : tareas de mantenimiento (.gitignore, dependencias, version.xml, puntero del submódulo, etc.)
+ test : agregar o corregir tests (sobre todo en dARKCore)
+ ci : cambios en GitHub Actions

Reglas:
- Descripción en **español**, en minúscula, en infinitivo o presente, sin punto final.
- Un commit = un cambio lógico. Si toca el core y el wrapper, son dos commits: el del core en su repo y un `chore` en el plugin que actualiza el submódulo.

Ejemplos reales:
```text
feat(importexport): importación de ARKs desde CSV en Herramientas > Importar/Exportar (E2-T3)
fix: impedir que la API REST modifique el estado del ARK y persistir dark::isExported
refactor: delegar en dARKCore las reglas de asignación, estados e importación
chore: actualizar puntero de dARKCore a origin/stable-php-7.4
docs: explicar por qué ArticleProvider consulta la base con el query builder
ci: probar con PHP 8.2 y 8.3 (OJS 3.5 requiere PHP 8.2)
```

## Ramas

**Plugin (`sedici/dARK`):**
- `stable-3_3`, `stable-3_4`, `stable-3_5`: versión liberada por entrega.
- `dev-3_3`, `dev-3_4`, `dev-3_5`: integración.
- Ramas de trabajo: `tipo/<tarea>-ojs3x`. Por ejemplo `feat/e2-t3-csv-import-ojs34`, `fix/critical-issues-ojs33`, `refactor/remove-is-public-exposed-ojs35`. Se crea **una por versión** con el mismo nombre base.

**Core (`dARKCore`):**
- `stable-php-7.4` y `dev-php-7.4`.
- Ramas de trabajo sin sufijo de versión: `feat/csv-import-service`, `fix/csv-record-immutable`.

## Flujo
1. Rama de trabajo desde `dev-*` (o `dev-php-7.4` en el core).
2. PR hacia `dev-*`. **Revisión del otro desarrollador** (prevista en el plan, dentro de la capacidad no asignada).
3. En el core, el CI corre PHPUnit en PHP 7.4, 8.0, 8.2 y 8.3 en cada PR.
4. Al cerrar una entrega: merge `dev-*` → `stable-*` en las tres ramas, que apuntan al **mismo commit o tag del core**, y se etiqueta.

## Tags
- Plugin: `e<N>-ojs3.3`, `e<N>-ojs3.4`, `e<N>-ojs3.5`.
- Core: `e<N>`.
- Existe `e1`. **Falta `e2`.** Ver [[Deuda técnica#Repositorios y versiones]].

## Submódulo del core
```bash
cd dARKCore && git fetch && git checkout <tag-o-commit> && cd ..
git add dARKCore && git commit -m "chore: actualizar puntero de dARKCore a <ref>"
```
Ojo: `make init` del sandbox vuelve a poner los worktrees en sus ramas `dev-*`. Ver [[¿Cómo usar?|Sandbox]].
