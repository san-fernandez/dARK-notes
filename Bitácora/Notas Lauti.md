https://registry.identifiers.org/registry/ark

¿El identificar dARK es de alguna manera diferente al identificador ARK o si estructuralmente son lo mismo?
+ Estructuralmente y a nivel de sintaxis, dARK es una extensión plenamente compatible con las reglas de asignación del esquema ARK. Usa los mismos componentes definidos en ARK
+ Tienen algunas diferencias
	+ En dARK, el shoulder es más amplio y sigue una convención rígida que integra dos subelementos explícitos: DNA y SNA. 
	+ El hombro del dARK se cierra con un **delimitador numérico** para fijar la longitud de ambas cadenas
	+ Ejemplo : en `MBLLPN3`, `MB` es el DNA, `LLPN` es el SNA y `3` es el delimitador
+ Un dARK es estructuralmente un ARK válido y estándar a nivel de sintaxis, pero utiliza una regla específica para empaquetar la autoridad institucional y de sección dentro del hombro

# Sobre los tests de dARKCore

+ Dependencias :
	+ PHPUnit
	+ Composer
+ Los tests se ejecutan mediante PHPUnit
+ Se corre `composer test` (atajo definido en composer.json) y que ejcuta phpunit
+ PHPUnit abre los test, busca las clases que extienden de TestCase y ejecuta los metodos publicos que empiecen con `test`
+ Para cada test se crea un objeto nuevo de la clase dARKCoreTest, llama a `setup()` , ejecuta el test y registra el resultado

+ Con el archivo tests.yml en dARKCore, se pueden implementar github actions en el repo

