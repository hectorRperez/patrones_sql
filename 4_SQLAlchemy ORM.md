# 4) SQLAlchemy ORM

ORM, que significa Object Relational Mapper, es la especialización del patrón de diseño de Data Mapper que aborda bases de datos relacionales como MySQL, Oracle y PostgreSQL. Como explicó Martin Fowler en el artículo, los mapeadores son responsables de mover los datos entre los objetos y una base de datos mientras los mantienen independientes entre sí. Como los lenguajes de programación orientados a objetos y las bases de datos relacionales estructuran los datos de diferentes maneras, necesitamos un código específico para traducir de un esquema a otro.

Por ejemplo, en un lenguaje de programación como Python, podemos crear una clase **Product** y una clase **Order** para relacionar tantas instancias como sea necesario de una clase a otra (es decir, Product puede contener una lista de instancias de Order y viceversa). Sin embargo, en las bases de datos relacionales, necesitamos tres entidades (tablas), una para conservar los productos, otra para mantener los pedidos y una tercera para relacionar (a través de una clave externa) productos y pedidos.

Como veremos en las siguientes secciones, SQLAlchemy ORM es una excelente solución de Data Mapper para traducir clases de Python a / desde tablas y mover datos entre instancias de estas clases y filas de estas tablas.

