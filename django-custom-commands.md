# Custom Commands

Django comes with a variety of command line utilities that can be either invoked using `django-admin.py` or the convenient `manage.py` script. A nice thing about it is that you can also add your own commands. Those management commands can be very handy when you need to interact with your application via command line using a terminal and it can also serve as an interface to execute cron jobs. In this tutorial you are going to learn how to code your own commands.

## Introduction

Just before we get started, let’s take a moment to familiarize with Django’s command line interface. You are probably already familiar with commands like `startproject`, `runserver` or `collectstatic`. To see a complete list of commands you can run the command below:

```bash
$ python manage.py help
```

## Advantage
The main advantage of `custom command` is that all Django machinery is loaded and ready to be used. That means you can import `models`, `execute queries` to the database `using Django’s ORM` and `interact` with all your project’s resources.


## Structure
We can create our own commands for our apps and include them in the list by creating a management/commands directory inside an app directory, like below:

```bash
< PROJECT ROOT >                          <-- project directory
 |-- poll/                                <-- app directory
 |    |-- management/
 |    |	   +-- __init__.py
 |    |    +-- commands/
 |    |         +-- __init__.py
 |    |         +-- my_custom_command.py  <-- module where command is going to live
 |    |-- migrations/
 |    |    +-- __init__.py
 |    |-- __init__.py
 |    |-- admin.py
 |    |-- apps.py
 |    |-- models.py
 |    |-- tests.py
 |    +-- views.py
 |-- core/
 |    |-- __init__.py
 |    |-- settings.py
 |    |-- urls.py
 |    |-- wsgi.py
 +-- manage.py
```

The name of the command file will be used to invoke using the command line utility. For example, if our command was called my_custom_command.py, then we will be able to execute it via:

```bash
$ python manage.py my_custom_command
```

## Let's start a simple project to explain more

what the custom command should look like:

`management/commands/what_time_is_it.py`:

```python
from django.core.management.base import BaseCommand
from django.utils import timezone

class Command(BaseCommand):
    help = 'Displays current time'

    def handle(self, *args, **kwargs):
        time = timezone.now().strftime('%X')
        self.stdout.write("It's %s" % time)
```

Django management command is composed by a class named `Command` which inherits from `BaseCommand`. The command code should be defined inside the `handle()` method.

This command can be executed as:

```bash
$ python manage.py what_time_is_it
```

Output:

```bash
It's 10:30:00
```
