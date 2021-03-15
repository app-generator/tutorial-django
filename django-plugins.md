# [Django Plugins](https://blog.appseed.us/django-tutorial-plugins/)

This is a tutorial on how to create the first third-party Django library or application.

## Introduction

[The Python Package Index (PyPI)](https://pypi.org/) has a vast range of packages you can use in your own Python programs.
Check out [Django Packages](https://djangopackages.org/) for existing reusable apps you could incorporate into your project. Django itself is also a normal Python package. This means that you can take existing Python packages or Django apps and compose them into your own web project. You only need to write the parts that make your project unique.

## Concern

It’s a lot of work to design, build, test and maintain a web application. Many Python and Django projects share common problems. Wouldn’t it be great if we could save some of this repeated work?
Let’s say you were starting a new project that needed a polls app like the one we’ve been working on. How do you make this app reusable?

## Structure

The layout of the package will be like this:

```bash
< PACKAGE ROOT >                          <-- package directory (django-polls)
 |-- polls/                               <-- app directory
 |    |-- migrations/
 |    |    -- __init__.py
 |    |-- static/                         <-- static files
 |    |-- templates/                      <-- template files
 |    |-- __init__.py
 |    |-- admin.py
 |    |-- apps.py
 |    |-- models.py
 |    |-- tests.py
 |    |-- views.py
 |-- LICENSE
 |-- MANIFEST.in
 |-- README.md
 |-- setup.cfg
 |-- setup.py
```

## Let's start a sample

Create your project (project should look like this):

```bash
< PROJECT ROOT >                          <-- project directory
 |-- polls/                               <-- app directory
 |    |-- migrations/
 |    |    -- __init__.py
 |    |-- static/                         <-- static files
 |    |    -- polls/
 |    |         -- styles.css
 |    |-- templates/                      <-- template files
 |    |    -- polls/
 |    |         -- index.html
 |    |-- __init__.py
 |    |-- admin.py
 |    |-- apps.py
 |    |-- models.py
 |    |-- tests.py
 |    |-- urls.py
 |    |-- views.py
 |-- core/
 |    |-- __init__.py
 |    |-- settings.py
 |    |-- urls.py
 |    |-- wsgi.py
 |-- manage.py
```

Now perhaps it is clearer why we chose to have separate template directories for the project and application: everything that is part of the polls application is in polls. It makes the application self-contained and easier to drop into a new project.

> The polls directory could now be copied into a new Django project and immediately reused. It’s not quite ready to be published though. For that, we need to package the app to make it easy for others to install.

## Packaging your app

- Create a parent directory for polls, outside of your Django project. Call this directory django-polls.

- Move the polls directory into the django-polls directory.

- Create a file `django-polls/README.md` with the following contents:

```text
# Polls

Polls is a Django app to conduct Web-based polls. For each question,
visitors can choose between a fixed number of answers.

## Quick start

1. Add "polls" to your INSTALLED_APPS setting like this::

    INSTALLED_APPS = [
        ...
        'polls',
    ]

2. Include the polls URLconf in your project urls.py like this::

    path('polls/', include('polls.urls')),

3. Run ``python manage.py migrate`` to create the polls models.

4. Start the development server and visit http://127.0.0.1:8000/admin/
   to create a poll (you'll need the Admin app enabled).

5. Visit http://127.0.0.1:8000/polls/ to participate in the poll.
```

- Create a `django-polls/LICENSE file`. **The code released publicly without a license is useless**. Django and many Django-compatible apps are distributed under the BSD license; however, you’re free to pick your own license. Just be aware that your licensing choice will affect who is able to use your code.

**Example (MIT License)**:
```text
MIT License

Copyright (c) 2021 App Generator

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```


- We’ll create `setup.cfg` and `setup.py` files which detail how to build and install the app:

`django-polls/setup.cfg`:
```metadata json
[metadata]
name = django-polls
version = 0.1
description = A Django app to conduct Web-based polls.
long_description = file: README.md
url = https://www.example.com/
author = Your Name
author_email = yourname@example.com
license = BSD-3-Clause  # Example license
classifiers =
    Environment :: Web Environment
    Framework :: Django
    Framework :: Django :: X.Y  # Replace "X.Y" as appropriate
    Intended Audience :: Developers
    License :: OSI Approved :: BSD License
    Operating System :: OS Independent
    Programming Language :: Python
    Programming Language :: Python :: 3
    Programming Language :: Python :: 3 :: Only
    Programming Language :: Python :: 3.6
    Programming Language :: Python :: 3.7
    Programming Language :: Python :: 3.8
    Topic :: Internet :: WWW/HTTP
    Topic :: Internet :: WWW/HTTP :: Dynamic Content

[options]
include_package_data = true
packages = find:
```

`django-polls/setup.py`:
```python
from setuptools import setup, find_packages

setup(
    packages=find_packages(),
    install_requires=[
        'Django',
        # ...
    ],
    include_package_data=True,
    zip_safe=False
)
```

- Only Python modules and packages are included in the package by default. To include additional files, we’ll need to create a `MANIFEST.in` file. The setuptools docs referred to in the previous step discuss this file in more details. To include the `templates`, `static`, the `README.md` and `LICENSE` file, create a `django-polls/MANIFEST.in` file with the following contents:

`django-polls/MANIFEST.in`:
```text
include LICENSE
include README.rst
recursive-include polls/static *
recursive-include polls/templates *
```

- It’s optional, but recommended, to include detailed documentation with your app. Create a directory (`django-polls/docs`) for future documentation. Add an additional line to `django-polls/MANIFEST.in`:

`django-polls/MANIFEST.in`:
```text
...
recursive-include docs *
```
> Note that the docs directory won’t be included in your package unless you add some files to it.

- Inside `django-polls` folder run following command to build your package:
```bash
$  python setup.py sdist
```
> This creates a directory called **dist** and builds your new package, django-polls-0.1.tar.gz.


For more information on packaging, see Python’s [Tutorial on Packaging and Distributing Projects](https://packaging.python.org/tutorials/packaging-projects/).
