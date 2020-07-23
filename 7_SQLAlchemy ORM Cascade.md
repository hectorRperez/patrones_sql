# SQLAlchemy ORM Cascade

Siempre que las filas en una tabla en particular se actualicen o eliminen, las filas en otras tablas también podrían sufrir cambios. Estos cambios pueden ser actualizaciones simples, que se denominan actualizaciones en cascada, o eliminaciones completas, conocidas como eliminaciones en cascada. Por ejemplo, supongamos que tenemos una tabla llamada shopping_carts, una tabla llamada products y una tercera llamada shopping_carts_products que conecta las dos primeras tablas. Si, por alguna razón, necesitamos eliminar filas de shopping_carts, también tendremos que eliminar las filas relacionadas de shopping_carts_products. De lo contrario, terminaremos con una gran cantidad de basura y referencias incompletas en nuestra base de datos.

Para hacer que este tipo de operación sea fácil de mantener, SQLAlchemy ORM permite a los desarrolladores mapear el comportamiento en cascada cuando usan construcciones de relación (). Así, cuando las operaciones se realizan en objetos primarios, los objetos secundarios también se actualizan / eliminan. La siguiente lista proporciona una breve explicación de las estrategias en cascada más utilizadas en SQLAlchemy ORM:

- save-update: Indica que cuando un objeto primario se guarda / actualiza, los objetos secundarios también se guardan / actualizan.

- delete: Indica que cuando se elimina un objeto principal, también se eliminarán los hijos de este objeto.

- delete-orphan:  Indica que cuando un objeto hijo pierde referencia a un padre, se eliminará.

- merge: Indica que las operaciones merge () se propagan de padres a hijos.

Si se necesita más información sobre esta función, [la documentación de SQLAlchemy proporciona un excelente capítulo sobre Cascadas.](https://docs.sqlalchemy.org/en/13/orm/cascades.html)