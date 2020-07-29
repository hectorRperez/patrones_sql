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

![Imagen del codigo](./data_type.png)


En el fragmento de código anterior, estamos definiendo una clase llamada Product que tiene seis propiedades. Echemos un vistazo a lo que hacen estas propiedades:

- **\_\_tablename__**  le dice a SQLAlchemy que las filas de la tabla de products deben asignarse a esta clase. 

- **id** identifica que esta es primary_key en la tabla y que su tipo es Integer.

- **title** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es String.

- **in_stock** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es booleano.

- **quantity** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es Integer.

- **prince** indica que una columna de la tabla tiene el mismo nombre de la propiedad y que su tipo es Numeric.

Los desarrolladores experimentados notarán que (generalmente) las bases de datos relacionales no tienen tipos de datos con estos nombres exactos. SQLAlchemy utiliza estos tipos como representaciones genéricas de las bases de datos compatibles y utiliza el dialecto configurado para comprender a qué tipos se traducen. Por ejemplo, en una base de datos PostgreSQL, el título se asignaría a una columna varchar.

# 6) SQLAlchemy Relationship Patterns

Ahora que sabemos qué es ORM y hemos analizado los tipos de datos, aprendamos cómo usar SQLAlchemy para asignar relaciones entre clases a relaciones entre tablas. SQLAlchemy admite cuatro tipos de relaciones: 

- [uno a muchos](https://docs.sqlalchemy.org/en/13/orm/basic_relationships.html#one-to-many)

- [muchos a uno](https://docs.sqlalchemy.org/en/13/orm/basic_relationships.html#many-to-one)

- [uno a uno](https://docs.sqlalchemy.org/en/13/orm/basic_relationships.html#one-to-one) 

- [muchos a muchos](http://docs.sqlalchemy.org/en/latest/orm/basic_relationships.html#many-to-many)

___

El primer tipo, One To Many, se usa para marcar que una instancia de una clase se puede asociar con muchas instancias de otra clase. Por ejemplo, en un motor de blog, una instancia de la clase **Article** podría asociarse con muchas instancias de la clase **Comment**. En este caso, mapearíamos las clases mencionadas y su relación de la siguiente manera:

![One To Many](./one_to_many.png)

___

El segundo tipo, Many To One, se refiere a la misma relación descrita anteriormente pero desde la otra perspectiva. Para dar un ejemplo diferente, digamos que queremos mapear la relación entre las instancias de **Tire** a una instancia de **Car**. Como muchas llantas pertenecen a un automóvil y este automóvil contiene muchas llantas, mapearíamos esta relación de la siguiente manera:

![Many To One](./many_to_one.png)

___

El tercer tipo, One to One, se refiere a las relaciones en las que una instancia de una clase particular solo puede asociarse con una instancia de otra clase, y viceversa. Como ejemplo, considere la relación entre **Person** y **MobilePhone**. Por lo general, una persona posee un teléfono móvil y este teléfono móvil pertenece solo a esta persona. Para mapear esta relación en SQLAlchemy, creamos el siguiente código:

![one to one](./one_to_one.png)

En este ejemplo, pasamos dos parámetros adicionales a la función de relación. El primero, uselist = False, hace que SQLAlchemy entienda que mobile_phone contendrá solo una instancia y no una matriz (múltiple) de instancias. El segundo, back_populate, indica a SQLAlchemy que complete el otro lado de la asignación. [La documentación oficial de la API de Relaciones ](https://docs.sqlalchemy.org/en/13/orm/relationship_api.html) proporciona una explicación completa de estos parámetros y también cubre otros parámetros no mencionados aquí.

___

El último tipo admitido por SQLAlchemy, Many To Many, se usa cuando las instancias de una clase en particular pueden tener cero o más asociaciones con instancias de otra clase. Por ejemplo, supongamos que estamos mapeando la relación de instancias de Student e instancias de Class en un sistema que administra una escuela. Como muchos estudiantes pueden participar en muchas clases, mapearíamos la relación de la siguiente manera:

![Many to Many](./many_to_many.png)

En este caso, tuvimos que crear una tabla auxiliar para mantener la asociación entre las instancias de Student y las instancias de Class, ya que esto no sería posible sin una tabla adicional. Tenga en cuenta que, para que SQLAlchemy conozca la tabla auxiliar, la pasamos en el parámetro secundario de la función de relación.

Los fragmentos de código anteriores muestran solo un subconjunto de las opciones de asignación admitidas por SQLAlchemy. En las siguientes secciones, vamos a echar un vistazo más profundo a cada uno de los patrones de relación disponibles. Además de eso, [la documentación oficial](http://docs.sqlalchemy.org/en/latest/orm/basic_relationships.html) es una gran referencia para aprender más sobre los patrones de relación en SQLAlchemy.

# SQLAlchemy ORM Cascade

Siempre que las filas en una tabla en particular se actualicen o eliminen, las filas en otras tablas también podrían sufrir cambios. Estos cambios pueden ser actualizaciones simples, que se denominan actualizaciones en cascada, o eliminaciones completas, conocidas como eliminaciones en cascada. Por ejemplo, supongamos que tenemos una tabla llamada shopping_carts, una tabla llamada products y una tercera llamada shopping_carts_products que conecta las dos primeras tablas. Si, por alguna razón, necesitamos eliminar filas de shopping_carts, también tendremos que eliminar las filas relacionadas de shopping_carts_products. De lo contrario, terminaremos con una gran cantidad de basura y referencias incompletas en nuestra base de datos.

Para hacer que este tipo de operación sea fácil de mantener, SQLAlchemy ORM permite a los desarrolladores mapear el comportamiento en cascada cuando usan construcciones de relación (). Así, cuando las operaciones se realizan en objetos primarios, los objetos secundarios también se actualizan / eliminan. La siguiente lista proporciona una breve explicación de las estrategias en cascada más utilizadas en SQLAlchemy ORM:

- save-update: Indica que cuando un objeto primario se guarda / actualiza, los objetos secundarios también se guardan / actualizan.

- delete: Indica que cuando se elimina un objeto principal, también se eliminarán los hijos de este objeto.

- delete-orphan:  Indica que cuando un objeto hijo pierde referencia a un padre, se eliminará.

- merge: Indica que las operaciones merge () se propagan de padres a hijos.

Si se necesita más información sobre esta función, [la documentación de SQLAlchemy proporciona un excelente capítulo sobre Cascadas.](https://docs.sqlalchemy.org/en/13/orm/cascades.html)

# SQLAlchemy Sessions

Las sesiones, en SQLAlchemy ORM, son la implementación del patrón de diseño de la Unidad de Trabajo. Como explicó Martin Fowler, una Unidad de Trabajo se utiliza para mantener una lista de objetos afectados por una transacción comercial y para coordinar la escritura de estos cambios. Esto significa que todas las modificaciones rastreadas por Sesiones (Unidades de Obras) se aplicarán juntas a la base de datos subyacente, o ninguna de ellas. En otras palabras, las sesiones se utilizan para garantizar la coherencia de la base de datos.

[La documentación oficial de SQLAlchemy ORM sobre Sesiones brinda una excelente explicación de cómo se rastrean los cambios](https://docs.sqlalchemy.org/en/13/orm/session_basics.html), cómo obtener sesiones y cómo crear sesiones ad-hoc. Sin embargo, en este artículo, utilizaremos la forma más básica de creación de sesión:

![session](./session.png)

Como podemos ver en el fragmento de código anterior, solo necesitamos un paso para obtener sesiones. Necesitamos crear una fábrica de sesiones que esté vinculada al motor SQLAlchemy. Después de eso, podemos emitir llamadas a esta fábrica de sesiones para obtener nuestras sesiones.

# Starting the Tutorial Project

 Podemos comenzar a asignar las clases de Python a las tablas de la base de datos. En este tutorial, asignaremos cuatro clases simples que representan películas, actores, especialistas y detalles de contacto. El siguiente diagrama ilustra las características de estas entidades y sus relaciones.

 ![Mapping Classes with SQLAlchemy](./modelo_basedatos.png)

 Para comenzar, crearemos un archivo llamado base.py en el directorio principal de nuestro proyecto y le agregaremos el siguiente código:

![Archivo base.py](./base.png)

Este código crea:

- un motor SQLAlchemy que interactuará con nuestra base de datos SQLite

- una fábrica de sesión ORM SQLAlchemy vinculada a este motor

- y una clase base para nuestras definiciones de clases. 

___

Ahora creemos y asignemos la clase Movie. Para hacer esto, creemos un nuevo archivo llamado movie.py y agreguemos el siguiente código:

![movie code](./movie_code.png)

La definición de esta clase y sus características de mapeo es bastante simple. Comenzamos haciendo que esta clase extienda la clase Base definida en el módulo base.py y luego le agregamos cuatro propiedades:

- \_\_tablename__ indica cuál es el nombre de la tabla que admitirá esta clase.

- id representa la llave primaria de la clase 

- title es de tipo String

- relase_date de tipo Date

___

La siguiente clase que crearemos y asignaremos es la clase Actor. Creemos un archivo llamado actor.py y agreguemos el siguiente código:

![clase actor](./clase_actor.png)

La definición de esta clase es bastante similar a la anterior. Las diferencias son que la clase **Actor** tiene un nombre en lugar de un **título**, un **cumpleaños** en lugar de una **fecha de lanzamiento**, y que apunta a una tabla llamada **actores** en lugar de **películas**.

Como muchas películas pueden tener muchos actores y viceversa, necesitaremos crear una relación de muchos a muchos entre estas dos clases. Creemos esta relación actualizando el archivo movie.py de la siguiente manera:

![code movie 2](./code_movie2.png)

La diferencia entre esta versión y la anterior es:

- Importamos 3 nuevas entidades: **Table**, **ForeignKey** y **relationship**

- creamos una tabla con el nombre **movie_actors_association** que conecta la filas de la tabla **actors** y las filas de **movies**

- Y agregamos la propiedad de **actors** a **Movie** y configuramos **movies_actors_association** como la tabla intermedia.

___

La próxima clase que crearemos es **Stuntman**. En nuestro tutorial, un **Actor** en particular tendrá solo un **Stuntman** y este **Stuntman** solo funcionará con este **Actor**. Esto significa que necesitamos crear la clase **Stuntman** y una relación uno a uno entre estas clases. Para lograr eso, creemos un archivo llamado **stuntman.py** y agreguemos el siguiente código:

![clase stuntman](./stuntman_code.png)

En esta clase, hemos definido que la propiedad del **actor** hace referencia a una instancia de **Actor** y que este **actor** obtendrá una propiedad llamada especialista que no es una lista (uselist = False). Es decir, cada vez que cargamos una instancia de Stuntman, SQLAlchemy también cargará y completará el Actor asociado con este especialista.

___

La cuarta y última clase que asignaremos en nuestro tutorial es **ContactDetails**. Las instancias de esta clase contendrán un **número de teléfono** y una **dirección** de un **Actor** en particular, y un Actor podrá tener muchos ContactDetails asociados. Por lo tanto, necesitaremos usar el patrón de relación Muchos a Uno para mapear esta asociación. Para crear esta clase y esta asociación, creemos un archivo llamado contact_details.py y agreguemos el siguiente código fuente:

![ContactDetails](./code_contactdetails.png)

Como podemos ver, crear una asociación Many To One es algo similar a crear una asociación One To One. La diferencia es que en este último le indicamos a SQLAlchemy que no use listas. Esta instrucción termina restringiendo la asociación a una sola instancia en lugar de una lista de instancias.

___


## Datos persistentes con SQLAlchemy ORM

Ahora que hemos creado nuestras clases, creemos un archivo llamado inserts.py y generemos algunas instancias de estas clases para que permanezcan en la base de datos. En este archivo, agreguemos el siguiente código:

![data part 1](./insert_1.png)
!['data part 2](./insert_2.png)

Este código se divide en 10 secciones. Vamos a inspeccionarlos:

1. La primera sección importa las clases que creamos, el motor SQLAlchemy, la clase Base, la fábrica de sesiones y **date** del módulo **datetime**.

2. La segunda sección le indica a SQLAlchemy que genere el esquema de la base de datos. Esta generación ocurre en base a las declaraciones que hicimos al crear las cuatro clases principales que componen nuestro tutorial.

3. The third section extracts a new session from the session factory.


4. La cuarta sección crea tres instancias de la clase **Movie**.

5. La quinta sección crea tres instancias de la clase **Actor**.

6. La sexta sección agrega actores a las películas. Tenga en cuenta que la película Pain & Gain hace referencia a dos actores: Dwayne Johnson y Mark Wahlberg.

7. La séptima sección crea instancias de **ContactDetails** y define a qué actores están asociadas estas instancias.

8. La octava sección define tres especialistas y también a qué actores están asociados estos especialistas.

9. La novena sección usa la sesión actual para guardar las películas, actores, detalles de contacto y especialistas creados. Tenga en cuenta que no hemos guardado explícitamente actores. Esto no es necesario porque SQLAlchemy, por defecto, usa la estrategia de cascada de guardar-actualizar.

10. La décima sección confirma la sesión actual en la base de datos y la cierra.

## Querying Data with SQLAlchemy ORM

Como veremos, consultar datos con SQLAlchemy ORM es bastante simple. Esta biblioteca proporciona una API intuitiva y fluida que permite a los desarrolladores escribir consultas que son fáciles de leer y mantener. En SQLAlchemy ORM, todas las consultas comienzan con un Objeto de consulta que se extrae de la sesión actual y que está asociado con una clase asignada particular. Para ver esta API en acción, creemos un archivo llamado queries.py y agreguemos el siguiente código fuente:

![data 1](./data_1.png)

El fragmento de código anterior, que se puede ejecutar con **python queries.py**, muestra lo fácil que es usar SQLAlchemy ORM para consultar datos. Para recuperar todas las películas de la base de datos, solo necesitábamos buscar una sesión de la fábrica de sesiones, usarla para obtener una consulta asociada con **Movie** y luego llamar a la función **all ()** en este objeto de consulta. La API de consulta proporciona docenas de funciones útiles como **all ()**. En la siguiente lista, podemos ver una breve explicación sobre los más importantes:

- count(): Devuelve el número total de filas de una consulta.

- filter(): Filters the query by applying a criteria.

- delete(): Elimina de la base de datos las filas que coinciden con una consulta.

- distinct(): Aplica una [declaración distinta](https://www.w3schools.com/sql/sql_distinct.asp) a una consulta.

- exists(): Agrega un [operador existente](https://www.w3schools.com/sql/sql_exists.asp) a una subconsulta.

- first(): Devuelve la primera fila de una consulta.

- get(): Devuelve la fila a la que hace referencia el parámetro de clave primaria pasado como argumento.

- join(): Crea una [unión SQL](https://www.w3schools.com/sql/sql_join.asp) en una consulta.

- limit(): Limita el número de filas devueltas por una consulta.

- order_by(): Establece un orden en las filas devueltas por una consulta.

Para explorar el uso de algunas de estas funciones, agreguemos el siguiente código al script queries.py:

![code_2](./data_2.png)

La quinta sección del script actualizado utiliza la función **filter ()** para buscar solo películas que se lanzaron después del 1 de enero de 2015. La sexta sección muestra cómo usar **join ()** para obtener instancias de **Movie** en las que participó el actor Dwayne Johnson. La séptima y última sección muestra el uso de las funciones **join ()** e **ilike ()** para recuperar actores que tienen casas en Glendale.

Ejecutar la nueva versión del script (python queries.py) ahora generará el siguiente resultado:

![data_3](./data_3.png)

Como podemos ver, el uso de la API es sencillo y genera un código que es legible. Para ver otras funciones compatibles con la API de consulta y su descripción, eche un vistazo a la [documentación oficial](https://docs.sqlalchemy.org/en/13/orm/query.html).


# [VER TUTORIAL ORIGINAL](https://auth0.com/blog/sqlalchemy-orm-tutorial-for-python-developers/)