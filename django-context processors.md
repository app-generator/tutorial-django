# Context Processors

The `context processor` has a simple interface. It’s a Python function that takes one argument, an HttpRequest object, and returns a dictionary that gets added to the template context. Each `context processor` must return a dictionary.

> Custom context processors can live anywhere in your codebase.


## Concern

When working with multiple and separate `views`, you may load different data objects into different views, but sometimes the use case requires you to have the same data available across the entire application by that, I mean across all templates of the application. What would you do in that case? A beginner might repeat the process of loading data into a template context for each view, which is not so clever. That’s exactly where custom context processors can be very useful.


## Use Case

You’re working on a course management system and you need to implement a search bar for available course subjects, and that search bar should be available across the entire application. 

> The Django gives you the ability to create a base template and then add others by simply extending the base one.

## Implementation

To create a custom context processor:

- add a new file inside your app folder. We call it custom_context_processor.py:

```bash
$ vim YOUR_APP/custom_context_processor.py
```

- Define a function (we call it model_x_items) then import your model and fetch your items from model:

```python
from .models import YOUR_MODEL

def model_x_items(request):
    return {
       ‘all_x_items’: YOUR_MODEL.objects.all(),
    }
```

> Explanations:
- model_x_items(request): A Python function that takes one argument which is a request object.
- all_x_items: A list of your model objects that returned as a dictionary.

- Now to make it accessible inside our templates, we need to add it inside our templates, context, so open `settings.py` and do like so:

```python
settings.py
# ...
TEMPLATES = [
 {
   'BACKEND': 'django.template.backends.django.DjangoTemplates',
   'DIRS': [os.path.join(BASE_DIR, 'templates')],
   'APP_DIRS': True,
   'OPTIONS': {
      'context_processors': [
      # ...
      'your_app.custom_context_processor.model_x_items',
   ],
  },
 },
]
# ...
```

- Now we've made the function available across all our templates. and it's time to display our data inside our template (TEMPLATE_NAME.html) to do this we'll make a html file inside our templates folder:

```bash
$ vim YOUR_APP/templates/YOUR_APP/TEMPLATE_NAME.py
```

```html
<!DOCTYPE html>
<html>
	<head>
		<!-- HEAD TAGS -->
	</head>
	
	<body>
		<u>
			{% for item in all_x_items %}
				<li><a href="{{ item.link }}">{{ s.name }}</a></li>
			{% endfor %}
		</u>
	</body>
</html>
```

Now your information will be available across all your application templates.
