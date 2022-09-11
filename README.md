![Logo](https://ricco-storage.s3.sa-east-1.amazonaws.com/django-deploy-assets/django-cover.png)
<div style="display: inline_block">
 <img align="center" alt="django-badge" src="https://img.shields.io/badge/Django-092E20?style=for-the-badge&logo=django&logoColor=green">
 <img align="center" alt="heroku-badge" src="https://img.shields.io/badge/Heroku-430098?style=for-the-badge&logo=heroku&logoColor=white">
 <img align="center" alt="python-badge" src="https://img.shields.io/badge/GIT-E44C30?style=for-the-badge&logo=git&logoColor=white">
</div>

# Deploy Django Aplication to Heroku

This is a basic setup for hosting django aplication in [Heroku](link).
I wrote this assuming you already have your app ready, and it's time for the big dreaded deploy. Rest assured, we will approach this step in a light and didactic way. Come on!

In case you got parachuted here, see how to create your application [here](https://www.django-rest-framework.org/tutorial/quickstart/).

##

## Hidding Environment Variables

Decouple helps you to organize your settings so that you can change parameters without having to redeploy your app.

Install using `pip install python-decouple`

See more for Python Decouple [here](https://pypi.org/project/python-decouple/).

Create an `.env` file at the root path and insert the following variables:
`SECRET_KEY=<your_secret_key> DEBUG=<value>` (Get this values from the `settings.py`)

## Changes in `settings.py`

Your file should look like this:

```
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

## Configuring the Data Base

Install Django Database using `pip install dj-database-url`

See more for Django Database Url [here](https://pypi.org/project/dj-database-url/).
and Add in your `Settings.py`

```
from dj_database_url import parse as dburl

default_dburl = 'sqlite:///' + os.path.join(BASE_DIR, 'db.sqlite3')

DATABASES = {'default': config(
    'DATABASE_URL', default=default_dburl, cast=dburl), }
```

Still in `settings.py` add
`STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')`

## Usage Django Static

You can use Django Static to serve your static files

Install Django Static using `pip install dj-static`
See more for Django Static [here](https://pypi.org/project/dj-static/).

Also add to the end of your `setting.py`

```
STATIC_ROOT = 'staticfiles'
STATIC_URL = '/static/'
```

## After that, we go to `wsgi.py`

```import os
from django.<project_name>.wsgi import get_wsgi_application
from dj_static import Cling

os.environ.setdefault("DJANGO_SETTINGS_MODULE", <project_name>.settings")

application = Cling(get_wsgi_application())
```

Obs: Also don't forget to check `"DJANGO_SETTINGS_MODULE"`. It is prone to frequent mistakes.

## Create a `requirements.txt`

Before running the command, make sure you have your `venv` enabled.

Aou can activate your venv through the command `. /<venv_name>/bin/activate`

After that run the command `pip freeze > requirements.txt`

See more for pip freeze docs [here](https://pip.pypa.io/en/stable/cli/pip_freeze/).

## Create a `runtime.txt`

Check the version of python installed on your machine with the command `python -v`
`python-3.9.12`

If you don't specify your version, Heroku will take care of providing the latest version of Python.
See the supported versions of Python in [Heroku Docs](https://devcenter.heroku.com/articles/python-support).

## Create a file `Procfile` and add the following code

Attention: make sure you don't put extra spaces or wrong information in this file, if that happens, your application will break.

`web: gunicorn <project_name>.wsgi --log-file -`

## Setting the allowed hosts

Include your address in ALLOWED_HOSTS directives in `settings.py`, domain only.
After configured it should look like this:

```
ALLOWED_HOSTS = ['<name_project>.herokuapp.com',
                 'localhost', '127.0.0.1']
```

## Heroku install config plugin

[Plugins](https://devcenter.heroku.com/articles/using-cli-plugins) allow developers to extend the functionality of the Heroku command interface, adding commands or features. To write your own plugin, read [Developing CLI Plugins](https://devcenter.heroku.com/articles/developing-cli-plugins#creating-the-plugin).

`heroku plugins:install heroku-config`

Sending configs from `.env` to Heroku using `heroku config:push -a`

To view your environment variables in Heroku, use
`heroku config`

## Creating the Git repository

initialize your repository `git init` and
create a file `.gitignore` with the following content:

```
build
coverage
.vscode <.yourIDE>
.DS_STORE
.env
.idea
tmp
files
*.sqlite3
db.sqlite3
staticfiles
```

## Publishing your application

Let's go to rice and beans:

`git add .`

`git commit -m 'Commit my super project🚀'`

`git push heroku master`

## Creating the Data Base

According to the [Heroku documentation](https://devcenter.heroku.com/articles/heroku-cli-commands), to run commands "on Heroku" we can
use the `heroku argument <your_command>`, to create our migrations let's try:

`heroku run python manage.py migrate`

## Creating the Django Admin user

Now let's create our super user for Django Admin using:
`heroku run python manage.py createsuperuser --email admin@example.com --username admin`

## Some extra tips

How to disable static file collection?
`heroku config:set DISABLE_COLLECTSTATIC=1`

How to changing a specific configuration?
`heroku config:set <config>=<value>`

###

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://github.com/rafaeelricco/django-deploy/blob/main/LICENSE)
