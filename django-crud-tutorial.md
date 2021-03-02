# Django CRUD (Create Read Update Delete) tutorial

Django is based on MVT (Model View Template) architecture and revolves around CRUD (Create Read Update Delete) operations. In general, CRUD means performing Create, Retrieve, Update and Delete operations on a table in a database. 

## Let’s discuss what about CRUD means:

![CRUD](https://raw.githubusercontent.com/app-generator/tutorial-django/main/media/crud.png)

- **Create:** Create or add new entries in a table in the database.
- **Read:** Read, retrieve, search, or view existing entries as a list (List View) or retrieve a particular entry in detail (Detail View).
- **Update:** Update or edit existing entries in a table in the database.
- **Delete:** Delete, deactivate, or remove existing entries in a table in the database.

## Django CRUD (Create, Retrieve, Update, Delete) Function Based Views

Consider a project named crudtest having an app named poll. Now let’s create a model of which we will be creating instances through our view. In `poll/models.py`.

```python
from django.db import models


class PollModel(models.Model): 
  
    title = models.CharField(max_length = 200) 
    description = models.TextField() 
  
    # renames the instances of the model with their title name 
    def __str__(self): 
        return self.title
```

After creating this model, we need to run two commands in order to create the table in the database:

```bash
$ python manage.py makemigrations
$ python manage.py migrate
```

Now we will create a Django ModelForm for this model. to do this create a file forms.py in `poll` folder.

```python
from django import forms 
from .models import PollModel


class PollForm(forms.ModelForm): 
  
    class Meta: 
        model = PollModel
        fields = ["title", "description"]  # specify fields to be used
```