
### Django data

* `User = get_user_model()` → get Django's **User model/table**.
* `User.objects.all()` → **get all users** from the database.
* `User(username="Haitem")` → create a **Python object**, not saved yet.
* `user.save()` → **save the object to the database**.
* `objects` → Django's **tool for communicating with the database**.

**Remember:**

```text
Python object → .save() → Database
Database → .objects.all() → Python objects
```


