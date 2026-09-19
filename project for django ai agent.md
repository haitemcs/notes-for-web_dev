CharField
→ relatively short text
→ name, email, title, status, category, room number

TextField
→ longer/free-form text
→ description, article, comments, document content


class Room(models.Model):
    room_number = models.CharField(max_length=10)
    room_type = models.CharField(max_length=20)





    created_at = models.DateTimeField(auto_now_add=True) #DB AUTO UPDATE THE field WHEN ITS created 
    updated_at = models.DateTimeField(auto_now=True) #db auto update this field to when its updated 





Django Document model: 

models.Model → creates a database table.
ForeignKey(User) → each document belongs to a user.
on_delete=CASCADE → deleting the user deletes their documents.
related_name='documents' → lets you do user.documents.all().
CharField → short text, such as the document title.
TextField → long text, such as document content.
BooleanField → True/False, here used for active.
auto_now_add=True → records when the document was created.
auto_now=True → records when the document was last updated.
settings.AUTH_USER_MODEL → refers to whichever user model Django is configured to use.






models.py
   ↓
makemigrations
   ↓
migration file
   ↓
migrate
   ↓
actual database table

So:

makemigrations = create instructions for changing the database

migrate = actually apply those instructions to the database.








if we wanna add an condition in the ORM in django 
we have to use the save() method 
how 
lets give it an example: 

class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.FloatField()
    discounted_price = models.FloatField(null=True)


  def save(self, *args, **kwargs): 
    discounted_price = price - price * 20%
super().save(*args, **kwargs)



 


