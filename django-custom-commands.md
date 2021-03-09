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

`management/commands/my_custom_command.py`:

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
$ python manage.py my_custom_command
```

Output:

```bash
It's 10:30:00
```

## Using Arguments

To handle arguments in our custom command we should define a method named `add_arguments`.

> Optional Arguments: The optional (and named) arguments can be passed in any order. In the example below you will find the definition of an argument named “prefix”, which will be used to compose the username field.

## Let's start an example to explain more

This example is a command that create random user instances. It takes an argument named `total`, which will define the number of users that will be created by the command.

`management/commands/generate_users.py`:

```python
from django.contrib.auth.models import User
from django.core.management.base import BaseCommand
from django.utils.crypto import get_random_string

class Command(BaseCommand):
    help = 'Create random users'

    def add_arguments(self, parser):
        parser.add_argument('total', type=int, help='Indicates the number of users to be created')
        parser.add_argument('-p', '--prefix', type=str, help='Define a username prefix', )  # Optional argument

    def handle(self, *args, **kwargs):
        total = kwargs['total']
        prefix = kwargs['prefix']

        for i in range(total):
            if prefix:
                username = '{prefix}_{random_string}'.format(prefix=prefix, random_string=get_random_string())
            else:
                username = get_random_string()
            User.objects.create_user(username=username, password='test_123456')
```

Usage:

```bash
$ python manage.py generate_users 3 --prefix custom_user
```

Or:

```bash
$ python manage.py generate_users 4 -p custom_user
```

## Flag Arguments

Another type of optional arguments are flags, which are used to handle boolean values. Let’s say we want to add an `--admin` flag, to instruct our command to create a super user or to create a regular user if the flag is not present.

`management/commands/create_users.py`:

```python
from django.contrib.auth.models import User
from django.core.management.base import BaseCommand
from django.utils.crypto import get_random_string

class Command(BaseCommand):
    help = 'Create random users'

    def add_arguments(self, parser):
        parser.add_argument('total', type=int, help='Indicates the number of users to be created')
        parser.add_argument('-p', '--prefix', type=str, help='Define a username prefix')
        parser.add_argument('-a', '--admin', action='store_true', help='Create an admin account')

    def handle(self, *args, **kwargs):
        total = kwargs['total']
        prefix = kwargs['prefix']
        admin = kwargs['admin']

        for i in range(total):
            if prefix:
                username = '{prefix}_{random_string}'.format(prefix=prefix, random_string=get_random_string())
            else:
                username = get_random_string()

            if admin:
                User.objects.create_superuser(username=username, email='', password='123')
            else:
                User.objects.create_user(username=username, email='', password='123')
```

Usage:

```bash
$ python manage.py create_users 2 --admin
```

Or:

```bash
$ python manage.py create_users 2 -a
```


## Arbitrary List of Arguments

Let’s create a new command now named `delete_users`. In this new command we will be able to pass a list of user ids and the command should delete those users from the database.

`management/commands/delete_users.py`:

```python
from django.contrib.auth.models import User
from django.core.management.base import BaseCommand

class Command(BaseCommand):
    help = 'Delete users'

    def add_arguments(self, parser):
        parser.add_argument('user_id', nargs='+', type=int, help='User ID')

    def handle(self, *args, **kwargs):
        users_ids = kwargs['user_id']

        for user_id in users_ids:
            try:
                user = User.objects.get(pk=user_id)
                user.delete()
                self.stdout.write('User "%s (%s)" deleted with success!' % (user.username, user_id))
            except User.DoesNotExist:
                self.stdout.write('User with id "%s" does not exist.' % user_id)
```

Usage:

```bash
$ python manage.py delete_users 1
```

Output:

```bash
User "SMl5ISqAsIS8 (1)" deleted with success!
```

> We can also pass a number of ids separated by spaces, so the command will delete the users in a single call

```bash
$ python manage.py delete_users 1 2 3 4
```

Output:

```bash
User with id "1" does not exist.
User "9teHR4Y7Bz4q (2)" deleted with success!
User "ABdSgmBtfO2t (3)" deleted with success!
User "BsDxOO8Uxgvo (4)" deleted with success!
```

## Styling

We could improve the previous example a little big by setting an appropriate color to the output message.

`management/commands/delete_users.py`:

```python
from django.contrib.auth.models import User
from django.core.management.base import BaseCommand

class Command(BaseCommand):
    help = 'Delete users'

    def add_arguments(self, parser):
        parser.add_argument('user_id', nargs='+', type=int, help='User ID')

    def handle(self, *args, **kwargs):
        users_ids = kwargs['user_id']

        for user_id in users_ids:
            try:
                user = User.objects.get(pk=user_id)
                user.delete()
                self.stdout.write(self.style.SUCCESS('User "%s (%s)" deleted with success!' % (user.username, user_id)))
            except User.DoesNotExist:
                self.stdout.write(self.style.WARNING('User with id "%s" does not exist.' % user_id))
```


Usage is the same as before, difference now is just the output:

```bash
$ python manage.py delete_users 3 4 5 6
```

Output:

![STYLING](https://raw.githubusercontent.com/app-generator/tutorial-django/main/media/styling1.png)


A list of all available styles, in form of a management command:

```python
from django.core.management.base import BaseCommand

class Command(BaseCommand):
    help = 'Show all available styles'

    def handle(self, *args, **kwargs):
        self.stdout.write(self.style.ERROR('error - A major error.'))
        self.stdout.write(self.style.NOTICE('notice - A minor error.'))
        self.stdout.write(self.style.SUCCESS('success - A success.'))
        self.stdout.write(self.style.WARNING('warning - A warning.'))
        self.stdout.write(self.style.SQL_FIELD('sql_field - The name of a model field in SQL.'))
        self.stdout.write(self.style.SQL_COLTYPE('sql_coltype - The type of a model field in SQL.'))
        self.stdout.write(self.style.SQL_KEYWORD('sql_keyword - An SQL keyword.'))
        self.stdout.write(self.style.SQL_TABLE('sql_table - The name of a model in SQL.'))
        self.stdout.write(self.style.HTTP_INFO('http_info - A 1XX HTTP Informational server response.'))
        self.stdout.write(self.style.HTTP_SUCCESS('http_success - A 2XX HTTP Success server response.'))
        self.stdout.write(self.style.HTTP_NOT_MODIFIED('http_not_modified - A 304 HTTP Not Modified server response.'))
        self.stdout.write(self.style.HTTP_REDIRECT('http_redirect - A 3XX HTTP Redirect server response other than 304.'))
        self.stdout.write(self.style.HTTP_NOT_FOUND('http_not_found - A 404 HTTP Not Found server response.'))
        self.stdout.write(self.style.HTTP_BAD_REQUEST('http_bad_request - A 4XX HTTP Bad Request server response other than 404.'))
        self.stdout.write(self.style.HTTP_SERVER_ERROR('http_server_error - A 5XX HTTP Server Error response.'))
        self.stdout.write(self.style.MIGRATE_HEADING('migrate_heading - A heading in a migrations management command.'))
        self.stdout.write(self.style.MIGRATE_LABEL('migrate_label - A migration name.'))
```

Output:

![STYLING](https://raw.githubusercontent.com/app-generator/tutorial-django/main/media/styling2.png)


## Advanced Usage

More advanced usage will boil down to knowing how to use the [argparse](https://docs.python.org/3/library/argparse.html) features. And of course, [Django’s official documentation on management commands](https://docs.djangoproject.com/en/2.1/howto/custom-management-commands/#command-objects) is the best resource.
