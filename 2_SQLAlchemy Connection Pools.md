# SQLAlchemy Connection Pools

La agrupación de conexiones es una de las implementaciones más tradicionales del patrón de agrupación de objetos. Las agrupaciones de objetos se usan como cachés de objetos preinicializados listos para usar. Es decir, en lugar de gastar tiempo para crear objetos que se necesitan con frecuencia (como conexiones a bases de datos), el programa busca un objeto existente del grupo, lo usa como lo desee y lo vuelve a colocar cuando haya terminado.

La razón principal por la cual los programas aprovechan este patrón de diseño es para mejorar el rendimiento. En el caso de las conexiones a la base de datos, abrir y mantener nuevas es costoso, requiere mucho tiempo y desperdicia recursos. Además de eso, este patrón permite una administración más fácil de la cantidad de conexiones que una aplicación podría usar simultáneamente.

Hay varias implementaciones del patrón de agrupación de conexiones disponibles en SQLAlchemy. Por ejemplo, crear un motor a través de la función create_engine () generalmente genera un QueuePool. Este tipo de grupo viene configurado con algunos valores predeterminados razonables, como un tamaño máximo de grupo de 5 conexiones.

Como los programas habituales listos para la producción deben anular estos valores predeterminados (para ajustar los grupos a sus necesidades), la mayoría de las diferentes implementaciones de grupos de conexiones proporcionan un conjunto similar de opciones de configuración. La siguiente lista muestra las opciones más comunes con sus descripciones:

### pool_size: Establece el número de conexiones que manejará el grupo.

### max_overflow: Especifica cuántas conexiones excedentes (en relación con pool_size) admite el grupo.

### pool_recycle: configura la antigüedad máxima (en segundos) de las conexiones en el grupo.

### pool_timeout: Identifies how many seconds the program will wait before giving up on getting a connection from the pool.