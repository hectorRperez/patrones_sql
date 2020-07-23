# SQLAlchemy Sessions

Las sesiones, en SQLAlchemy ORM, son la implementación del patrón de diseño de la Unidad de Trabajo. Como explicó Martin Fowler, una Unidad de Trabajo se utiliza para mantener una lista de objetos afectados por una transacción comercial y para coordinar la escritura de estos cambios. Esto significa que todas las modificaciones rastreadas por Sesiones (Unidades de Obras) se aplicarán juntas a la base de datos subyacente, o ninguna de ellas. En otras palabras, las sesiones se utilizan para garantizar la coherencia de la base de datos.

[La documentación oficial de SQLAlchemy ORM sobre Sesiones brinda una excelente explicación de cómo se rastrean los cambios](https://docs.sqlalchemy.org/en/13/orm/session_basics.html), cómo obtener sesiones y cómo crear sesiones ad-hoc. Sin embargo, en este artículo, utilizaremos la forma más básica de creación de sesión:

~~~
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

# create an engine
engine = create_engine('postgresql://usr:pass@localhost:5432/sqlalchemy')

# create a configured "Session" class
Session = sessionmaker(bind=engine)

# create a Session
session = Session()
~~~

Como podemos ver en el fragmento de código anterior, solo necesitamos un paso para obtener sesiones. Necesitamos crear una fábrica de sesiones que esté vinculada al motor SQLAlchemy. Después de eso, podemos emitir llamadas a esta fábrica de sesiones para obtener nuestras sesiones.