| Category         | Option / Field                 | Simple definition                                                    |
| ---------------- | ------------------------------ | -------------------------------------------------------------------- |
| **Field type**   | `CharField`                    | Short text/string                                                    |
| **Field type**   | `TextField`                    | Long/free-form text                                                  |
| **Field type**   | `IntegerField`                 | Whole number: `1`, `25`, `-4`                                        |
| **Field type**   | `FloatField`                   | Decimal/floating-point number                                        |
| **Field type**   | `DecimalField`                 | Precise decimal number, useful for money                             |
| **Field type**   | `BooleanField`                 | `True` or `False`                                                    |
| **Field type**   | `DateField`                    | Date only                                                            |
| **Field type**   | `DateTimeField`                | Date + time                                                          |
| **Field type**   | `TimeField`                    | Time only                                                            |
| **Field type**   | `EmailField`                   | Email address                                                        |
| **Field type**   | `URLField`                     | URL/web address                                                      |
| **Field type**   | `FileField`                    | Uploaded file                                                        |
| **Field type**   | `ImageField`                   | Uploaded image                                                       |
| **Field type**   | `JSONField`                    | Stores JSON data                                                     |
| **Field type**   | `ForeignKey`                   | Many objects can belong to one other object                          |
| **Field type**   | `OneToOneField`                | One object is connected to exactly one other object                  |
| **Field type**   | `ManyToManyField`              | Many objects can connect to many other objects                       |
| **Option**       | `default=`                     | Value used automatically when no value is provided                   |
| **Option**       | `null=True`                    | Database is allowed to store `NULL`                                  |
| **Option**       | `blank=True`                   | Field is allowed to be empty during validation/forms                 |
| **Option**       | `max_length=`                  | Maximum number of characters                                         |
| **Option**       | `unique=True`                  | No two database records can have the same value                      |
| **Option**       | `primary_key=True`             | Makes this field the table's primary key                             |
| **Option**       | `db_index=True`                | Creates a database index to make searches faster                     |
| **Option**       | `choices=`                     | Restricts the field to predefined choices                            |
| **Option**       | `editable=False`               | Prevents normal editing through forms/admin                          |
| **Option**       | `verbose_name=`                | Human-readable name for the field                                    |
| **Option**       | `help_text=`                   | Extra explanation displayed in forms/admin                           |
| **Option**       | `auto_now_add=True`            | Automatically sets the time when the object is **created**           |
| **Option**       | `auto_now=True`                | Automatically updates the time whenever the object is **saved**      |
| **Relationship** | `on_delete=models.CASCADE`     | Delete related objects when the referenced object is deleted         |
| **Relationship** | `on_delete=models.PROTECT`     | Prevent deletion if related objects exist                            |
| **Relationship** | `on_delete=models.SET_NULL`    | Set the relationship to `NULL` when the referenced object is deleted |
| **Relationship** | `on_delete=models.SET_DEFAULT` | Replace the relationship with its default value                      |
| **Relationship** | `related_name=`                | Name used to access the relationship from the other side             |
