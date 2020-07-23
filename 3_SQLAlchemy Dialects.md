# SQLAlchemy Dialects

Como SQLAlchemy es una fachada que permite a los desarrolladores de Python crear aplicaciones que se comuniquen con diferentes motores de bases de datos a través de la misma API, debemos utilizar Dialectos. La mayoría de las bases de datos relacionales populares disponibles se adhieren al estándar SQL (lenguaje de consulta estructurado), pero también introducen variaciones propietarias. Estas variaciones son las únicas responsables de la existencia de dialectos.

Por ejemplo, supongamos que queremos obtener las primeras diez filas de una tabla llamada personas. Si nuestros datos estuvieran en poder de un motor de base de datos de Microsoft SQL Server, SQLAlchemy necesitaría emitir la siguiente consulta:

~~~
SELECT TOP 10 * FROM people;
~~~

### Pero, si nuestros datos persistieron en la instancia de MySQL, entonces SQLAlchemy necesitaría emitir:

~~~
SELECT * FROM people LIMIT 10;
~~~

Por lo tanto, para saber con precisión qué consulta emitir, SQLAlchemy debe conocer el tipo de base de datos con la que se está tratando. Esto es exactamente lo que hacen los dialectos. Hacen que SQLAlchemy sea consciente del dialecto que necesita hablar.

En esencia, SQLAlchemy incluye la siguiente lista de dialectos:


- Firebird
- Microsoft SQL Server
- MySQL
- Oracle
- PostgreSQL
- SQLite
- Sybase