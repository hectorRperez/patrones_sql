# Guia de Patrones de relaciones con SQLAlchemy

### Esto es una guía para trabajar con SQLAlchemy, entender los patrones de relaciones entre clases 

# 1) SQLAlchemy Engines 

## SQLAlchemy Engines

Siempre que queramos usar SQLAlchemy para interactuar con una base de datos, necesitamos crear un motor. Los motores, en SQLAlchemy, se utilizan para gestionar dos factores cruciales: piscinas y dialectos. Las siguientes dos secciones explicarán cuáles son estos dos conceptos, pero por ahora es suficiente decir que SQLAlchemy los usa para interactuar con las funciones DBAPI.

~~~
from sqlalchemy import create_engine
engine = create_engine('postgresql://usr:pass@localhost:5432/sqlalchemy')
~~~

Este ejemplo crea un motor PostgreSQL para comunicarse con una instancia que se ejecuta localmente en el puerto 5432 (el predeterminado). También define que usará ***usr*** y ***pass*** como credenciales para interactuar con la base de datos sqlalchemy. Tenga en cuenta que crear un motor no se conecta a la base de datos al instante. Este proceso se pospone cuando es necesario (como cuando enviamos una consulta o cuando creamos / actualizamos una fila en una tabla).

Dado que SQLAlchemy se basa en la especificación DBAPI para interactuar con las bases de datos, se admiten los sistemas de administración de bases de datos más comunes disponibles. PostgreSQL, MySQL, Oracle, Microsoft SQL Server y SQLite son ejemplos de motores que podemos usar junto con SQLAlchemy. Para obtener más información sobre las opciones disponibles para crear motores SQLAlchemy, [consulte la documentación oficial](https://docs.sqlalchemy.org/en/13/core/engines.html)


**Nota**
___
DBAPI: (un acrónimo de DataBase API) se creó para especificar cómo los módulos de Python que se integran con las bases de datos deberían exponer sus interfaces. Aunque no interactuaremos directamente con esta API (usaremos SQLAlchemy como fachada), es bueno saber que define cómo deben comportarse las funciones comunes como conectar, cerrar, confirmar y deshacer. En consecuencia, siempre que usemos un módulo Python que se adhiera a la especificación, podemos estar seguros de que encontraremos estas funciones y que se comportarán como se espera.
___


# 2) SQLAlchemy Connection Pools

## SQLAlchemy Connection Pools

La agrupación de conexiones es una de las implementaciones más tradicionales del patrón de agrupación de objetos. Las agrupaciones de objetos se usan como cachés de objetos preinicializados listos para usar. Es decir, en lugar de gastar tiempo para crear objetos que se necesitan con frecuencia (como conexiones a bases de datos), el programa busca un objeto existente del grupo, lo usa como lo desee y lo vuelve a colocar cuando haya terminado.

La razón principal por la cual los programas aprovechan este patrón de diseño es para mejorar el rendimiento. En el caso de las conexiones a la base de datos, abrir y mantener nuevas es costoso, requiere mucho tiempo y desperdicia recursos. Además de eso, este patrón permite una administración más fácil de la cantidad de conexiones que una aplicación podría usar simultáneamente.

Hay varias implementaciones del patrón de agrupación de conexiones disponibles en SQLAlchemy. Por ejemplo, crear un motor a través de la función create_engine () generalmente genera un QueuePool. Este tipo de grupo viene configurado con algunos valores predeterminados razonables, como un tamaño máximo de grupo de 5 conexiones.

Como los programas habituales listos para la producción deben anular estos valores predeterminados (para ajustar los grupos a sus necesidades), la mayoría de las diferentes implementaciones de grupos de conexiones proporcionan un conjunto similar de opciones de configuración. La siguiente lista muestra las opciones más comunes con sus descripciones:

- pool_size: Establece el número de conexiones que manejará el grupo.

- max_overflow: Especifica cuántas conexiones excedentes (en relación con pool_size) admite el grupo.

- pool_recycle: configura la antigüedad máxima (en segundos) de las conexiones en el grupo.

- pool_timeout: Identifies how many seconds the program will wait before giving up on getting a connection from the pool.

# 3) SQLAlchemy Dialects

### SQLAlchemy Dialects

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

# 4) SQLAlchemy ORM

ORM, que significa Object Relational Mapper, es la especialización del patrón de diseño de Data Mapper que aborda bases de datos relacionales como MySQL, Oracle y PostgreSQL. Como explicó Martin Fowler en el artículo, los mapeadores son responsables de mover los datos entre los objetos y una base de datos mientras los mantienen independientes entre sí. Como los lenguajes de programación orientados a objetos y las bases de datos relacionales estructuran los datos de diferentes maneras, necesitamos un código específico para traducir de un esquema a otro.

Por ejemplo, en un lenguaje de programación como Python, podemos crear una clase de Producto y una clase de Orden para relacionar tantas instancias como sea necesario de una clase a otra (es decir, el Producto puede contener una lista de instancias de Orden y viceversa). Sin embargo, en las bases de datos relacionales, necesitamos tres entidades (tablas), una para conservar los productos, otra para mantener los pedidos y una tercera para relacionar (a través de una clave externa) productos y pedidos.

Como veremos en las siguientes secciones, SQLAlchemy ORM es una excelente solución de Data Mapper para traducir clases de Python a / desde tablas y mover datos entre instancias de estas clases y filas de estas tablas.

# 5) SQLAlchemy Data Types

Al usar SQLAlchemy, podemos estar seguros de que obtendremos soporte para los tipos de datos más comunes que se encuentran en las bases de datos relacionales. Por ejemplo, los valores booleanos, fechas, horas, cadenas y valores numéricos son solo un subconjunto de los tipos para los que SQLAlchemy proporciona abstracciones. Además de estos tipos básicos, SQLAlchemy incluye soporte para algunos tipos específicos de proveedores (como JSON) y también permite a los desarrolladores crear tipos personalizados y redefinir los existentes.

Para comprender cómo usamos los tipos de datos SQLAlchemy para asignar propiedades de las clases de Python a columnas en una tabla de base de datos de relaciones, analicemos el siguiente ejemplo:

![Imagen del codigo](./codigo.png)


En el fragmento de código anterior, estamos definiendo una clase llamada Product que tiene seis propiedades. Echemos un vistazo a lo que hacen estas propiedades:

- **\_\_tablename__**  le dice a SQLAlchemy que las filas de la tabla de products deben asignarse a esta clase. 

- **id** identifica que esta es primary_key en la tabla y que su tipo es Integer.

- **title** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es String.

- **in_stock** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es booleano.

- **quantity** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es Integer.

- **prince** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es Numeric.

Los desarrolladores experimentados notarán que (generalmente) las bases de datos relacionales no tienen tipos de datos con estos nombres exactos. SQLAlchemy utiliza estos tipos como representaciones genéricas de las bases de datos compatibles y utiliza el dialecto configurado para comprender a qué tipos se traducen. Por ejemplo, en una base de datos PostgreSQL, el título se asignaría a una columna varchar.