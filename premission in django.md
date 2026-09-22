### Django permissions — short summary

* `get_user_model()` → gets the project's actual **User model**.
* `User.objects.all()` → gets all users from the database as a **QuerySet**.
* `is_staff=True` → user can access the Django admin (if otherwise permitted).
* `is_superuser=True` → user effectively has **all permissions**.
* `user_permissions` → permissions directly assigned to that specific user.
* `Permission` → represents an ability, such as:

  * `view_document`
  * `add_document`
  * `change_document`
  * `delete_document`
* `.filter()` → selects objects matching conditions.
* `.remove()` → removes direct permissions.
* `.set()` → replaces the user's direct permissions with the given set.
* `.exclude()` → removes matching objects from a QuerySet.
* `user.has_perm(...)` → checks whether the user **actually has a permission**.

### The main idea

```text
User
 ↓
Permissions / Groups
 ↓
What the user is allowed to do
```

For example:

```text
Ethan
 ✓ view_document
 ✓ add_document
 ✓ change_document
 ✗ delete_document
```

**Authentication = "Who are you?"**
**Authorization = "What are you allowed to do?"**

Your notebook is teaching you the **authorization** part.


name="Ethan"              # exact
name__icontains="ethan"  # contains, case-insensitive
name__startswith="Eth"   # starts with
name__endswith="an"      # ends with
age__gt=18                # greater than
age__gte=18               # greater/equal
age__lt=18                # less than
age__lte=18               # less/equal



age__gt=18

age   __   gt
 ↑          ↑
WHAT?      HOW?

what he need to search .
wht the condtion .
its adinition








thats an example : 

Problem: Document permissions

You have a staff user:

staff_u = User.objects.filter(
    is_superuser=False,
    is_staff=True
).first()

Your goal is to give this user permission to:

✅ View documents
✅ Add documents
✅ Change documents
❌ Delete documents
Your task

Write the Django ORM code that:

Gets all Document permissions.
Removes the delete_document permission.
Assigns the remaining permissions to staff_u.
Verifies the result using has_perm().

You should end up with:

view_document  → True
add_document   → True
change_document → True
delete_document → False








from django.contrib.auth import get_user_model 
from django.contrib.auth.models import permission 

user = get_user_model()

#first of all we have to get the stuff user 

staff_u = User.objects.filter(
  is_superuser = False, 
  is_staff = True 
).first()


docs_qs = Permission.objects.filter(

codename__endswith="document"
)




