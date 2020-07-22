# SQLAlchemy Engines

Siempre que queramos usar SQLAlchemy para interactuar con una base de datos, necesitamos crear un motor. Los motores, en SQLAlchemy, se utilizan para gestionar dos factores cruciales: piscinas y dialectos. Las siguientes dos secciones explicarán cuáles son estos dos conceptos, pero por ahora es suficiente decir que SQLAlchemy los usa para interactuar con las funciones DBAPI.

~~~
from sqlalchemy import create_engine
engine = create_engine('postgresql://usr:pass@localhost:5432/sqlalchemy')
~~~

### Este ejemplo crea un motor PostgreSQL para comunicarse con una instancia que se ejecuta localmente en el puerto 5432 (el predeterminado). También define que usará ***usr*** y ***pass*** como credenciales para interactuar con la base de datos sqlalchemy. Tenga en cuenta que crear un motor no se conecta a la base de datos al instante. Este proceso se pospone cuando es necesario (como cuando enviamos una consulta o cuando creamos / actualizamos una fila en una tabla).

Dado que SQLAlchemy se basa en la especificación DBAPI para interactuar con las bases de datos, se admiten los sistemas de administración de bases de datos más comunes disponibles. PostgreSQL, MySQL, Oracle, Microsoft SQL Server y SQLite son ejemplos de motores que podemos usar junto con SQLAlchemy. Para obtener más información sobre las opciones disponibles para crear motores SQLAlchemy, [consulte la documentación oficial](https://docs.sqlalchemy.org/en/13/core/engines.html)


**Nota**
___
DBAPI: (un acrónimo de DataBase API) se creó para especificar cómo los módulos de Python que se integran con las bases de datos deberían exponer sus interfaces. Aunque no interactuaremos directamente con esta API (usaremos SQLAlchemy como fachada), es bueno saber que define cómo deben comportarse las funciones comunes como conectar, cerrar, confirmar y deshacer. En consecuencia, siempre que usemos un módulo Python que se adhiera a la especificación, podemos estar seguros de que encontraremos estas funciones y que se comportarán como se espera.
___