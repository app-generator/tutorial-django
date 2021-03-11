# Django Software Architecture

In this tutorial, we will talk about the Django MVT architecture and how is it different from the long-existing MVC architecture.

## Introduction

Django is a Python based free and open-source web framework that follows the MVT (Model View Template) architectural pattern. 
Django's primary goal is to ease the creation of complex, database-driven websites. The framework emphasizes reusability and "pluggability" of components, less code, low coupling, rapid development, and the principle of don't repeat yourself. Python is used throughout, even for settings, files, and data models. Django also provides an optional administrative create, read, update and delete interface that is generated dynamically through introspection and configured via admin models.

## MVT Architecture

MVT Structure has the following three parts:

- **Model:** The `Model` is going to act as the interface of your data. It is responsible for maintaining data. It is the logical data structure behind the entire application and is represented by a database (generally relational databases such as MySql, Postgres).

- **View:** The `View` is the user interface what you see in your browser when you render a website. It is represented by HTML/CSS/Javascript and Jinja files.

- **Template:** A `Template` consists of static parts of the desired HTML output as well as some special syntax describing how dynamic content will be inserted.

> Although Django follows MVC pattern but maintains it's own conventions. So, control is handled by the framework itself. There is no separate controller and complete application is based on Model View and Template. That's why it is called MVT application.


See the following graph that shows the MVT based control flow:

![ARCHITECTURE1](https://raw.githubusercontent.com/app-generator/tutorial-django/main/media/architecture_1.png)

Here, a user requests for a resource to the Django, Django works as a controller and check to the available resource in URL. If URL maps, a view is called that interact with model and template, it renders a template. Django responds back to the user and sends a template as a response.

## Project Structure

A Django Project when initialised contains basic files by default such as `manage.py`, `view.py`, etc. A simple project structure is enough to create a single page application. Here are the major files and there explanations. Inside the `geeks_site` folder (project folder) there will be following files:

![ARCHITECTURE1](https://raw.githubusercontent.com/app-generator/tutorial-django/main/media/architecture_2.png)

**manage.py:** This file is used to interact with your project via the command line (start the server, sync the database… etc). For getting the full list of command that can be executed by manage.py type this code in the terminal:

```bash
$ python manage.py help
```





