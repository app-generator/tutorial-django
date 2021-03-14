# Django CRUD (Create Read Update Delete) tutorial

Django is based on MVT (Model View Template) architecture and revolves around CRUD (Create Read Update Delete) operations. In general, CRUD means performing Create, Read, Update and Delete operations on a table in a database. 

## Let’s discuss what about CRUD means:

![CRUD](https://raw.githubusercontent.com/app-generator/tutorial-django/main/media/crud.png)

- **Create:** Create or add new entries in a table in the database.
- **Read:** Read, retrieve, search, or view existing entries as a list (List View) or retrieve a particular entry in detail (Detail View).
- **Update:** Update or edit existing entries in a table in the database.
- **Delete:** Delete, deactivate, or remove existing entries in a table in the database.

## Let's start a simple project to explain more:

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
        fields = ['title', 'description']  # specify fields to be used
```

### Now let's implement each of the CRUD sections:

#### Create View

It's just like taking an input from a user and storing it in a specific table to create an instance of a table in the database.

`poll/views.py`:
```python
from django.shortcuts import render
from .models import PollModel
from .forms import PollForm


def create_view(request):
    context = {}  # dictionary for initial data with field names as keys

    form = PollForm(request.POST or None)
    if form.is_valid():
        form.save()
    
    context['form'] = form
    return render(request, "create_view.html", context)
```

Then create a template in `templates/create_view.html`:

```html
<form method="POST"> 
    {% csrf_token %} <!-- CSRF token for security -->
  
    {{ form.as_p }} 
      
    <input type="submit" value="Create"> 
</form>
```

<br>

> Read is basically divided into two types of views, List View and Detail View.

#### List View
 
It's used to display multiple types of data or list all or particular instances of a table from the database in a particular order  on a single page or view.

`poll/views.py`:
```python
from django.shortcuts import render  
from .models import PollModel


def list_view(request):
    context = {}   # dictionary for initial data with field names as keys
    context["items"] = PollModel.objects.all()  # add the dictionary during initialization
    return render(request, "list_view.html", context)
```

Then create a template in `templates/list_view.html`:

```html
<div class="main"> 
    
    {% for item in items %}
	    <p>{{ item.title }}</p><br/> 
	    <p>{{ item.description }}</p><br/>
	    <hr> 
  	{% endfor %}

</div>
```

#### Detail View

It's used to display multiple types of data or  a particular instnace of a table from the database with all the necessary details on a single page.

`poll/urls.py`:
```python
from django.urls import path   
from .views import detail_view 
  
urlpatterns = [ 
    path('poll/<int:id>/', detail_view, name='detail_poll'), 
]
```

`poll/views.py`:
```python
from django.shortcuts import render 
from .models import PollModel 

def detail_view(request, id): 
    context = {}  # dictionary for initial data with field names as keys  
    context["data"] = PollModel.objects.get(id=id)
    return render(request, "detail_view.html", context)
```

Then create a template in `templates/detail_view.html`:

```html
<div class="main"> 
    
    {{ data.title }}<br/> 
    {{ data.description }}

</div>
```

#### Update View

It's used to update entries for a particular instance of a table from the database.

`poll/views.py`:
```python
from django.shortcuts import get_object_or_404, render, redirect
from .models import PollModel 
from .forms import PollForm 


# after updating it will redirect to detail_View 
def detail_view(request, id): 
    context = {}  # dictionary for initial data with field names as keys
    context["data"] = PollModel.objects.get(id=id)  # add the dictionary during initialization
    return render(request, "detail_view.html", context)
  
# update view for details 
def update_view(request, id): 
    context = {}  # dictionary for initial data with field names as keys
    obj = get_object_or_404(PollModel, id=id)  # fetch the object related to passed id

    # pass the object as instance in form 
    form = PollForm(request.POST or None, instance=obj) 

    # save the data from the form and redirect to detail_view 
    if form.is_valid(): 
        form.save() 
        return redirect("detail_poll", id)

    context["form"] = form  # add form dictionary to context
    return render(request, "update_view.html", context)
```

Then create a template in `templates/update_view.html`:

```html
<div class="main"> 

    <form method="POST"> 
        {% csrf_token %} <!-- CSRF token for security -->
  
        {{ form.as_p }} 
  
        <input type="submit" value="Update">
    </form> 
  
</div>
```

#### Delete View

It is used to delete a particular instance of a table from the database.

`poll/views.py`:
```python
from django.shortcuts import get_object_or_404, render, redirect
from .models import PollModel 


# delete view for details 
def delete_view(request, id): 
    context = {}  # dictionary for initial data with field names as keys 
    obj = get_object_or_404(PollModel, id=id)  # fetch the object related to passed id 

    if request.method =="POST": 
        obj.delete()  # delete object
        return redirect("list_view")  # after deleting redirect to list view
  
    return render(request, "delete_view.html", context)
```

Now a url `poll/urls.py` mapping to this view:

```python
from django.urls import path 
from .views import delete_view 

urlpatterns = [ 
    path('poll/<int:id>/delete/', delete_view, name='delete_poll'), 
]
```

Then create a template in `templates/delete_view.html`:

```html
<div class="main"> 

    <form method="POST"> 
        {% csrf_token %} 

        <p>Are you want to delete this item ?</p>
        
        <input type="submit" value="Yes" /> 
    </form>

    <a href="/">Cancel </a> 

</div>
```

Well, we have successfully created a CRUD application using Django.