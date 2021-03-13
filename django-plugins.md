# django plugins

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
< PACKAGE ROOT >                          <-- package directory
 |-- poll/                                <-- app directory
 |    |-- migrations/
 |    |    +-- __init__.py
 |    |-- static/                         <-- static files
 |    |-- templates/                      <-- template files
 |    |-- __init__.py
 |    |-- admin.py
 |    |-- apps.py
 |    |-- models.py
 |    |-- tests.py
 |    +-- views.py
 |-- LICENSE
 |-- MANIFEST.in
 |-- README.md
 +-- setup.py
```

## Let's start a sample

Create your project (project should look like this):

```bash
< PROJECT ROOT >                          <-- project directory
 |-- polls/                               <-- app directory
 |    |-- migrations/
 |    |    +-- __init__.py
 |    |-- static/                         <-- static files
 |    |    +-- polls/
 |    |         +-- styles.css
 |    |-- templates/                      <-- template files
 |    |    +-- polls/
 |    |         +-- index.html
 |    |-- __init__.py
 |    |-- admin.py
 |    |-- apps.py
 |    |-- models.py
 |    |-- tests.py
 |    |-- urls.py
 |    +-- views.py
 |-- core/
 |    |-- __init__.py
 |    |-- settings.py
 |    |-- urls.py
 |    |-- wsgi.py
 +-- manage.py
```

Now perhaps it is clearer why we chose to have separate template directories for the project and application: everything that is part of the polls application is in polls. It makes the application self-contained and easier to drop into a new project.

> The polls directory could now be copied into a new Django project and immediately reused. It’s not quite ready to be published though. For that, we need to package the app to make it easy for others to install.

## Packaging your app