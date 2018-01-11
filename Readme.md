# Learn Django
# Install
```
pip install django==2.0.1
```

# Tutorial
## Writing the first Django app
## part I
This tutorial will work you through the creation of a basic poll application
It'll consist of two parts:
- A public site that lets people view polls and vote in them.
- An admin site that lets you add, change, and delete polls.

## Creating a project
```
cd into a directory you want creat a django app

django-admin startproject mysite
```
There dir and files created by startproject
```
Mysite/
    manage.py
    mysite/
	__init__.py
	settings.py
	urls.py
	wsgi.py
```
## The development server
```
python manage.py runserver
```
Then you can visit http://127.0.0.1:8000/ to see the "Congratulations!" page.
Now that your environment a project is set up, you're set to start doing work.
## Creating the Polls app
Projects vs. apps
```
What's the difference between a project and an app? 
An app is a Web application that does something -e.g., a Weblog system, a database of public records or a simple poll app.
A project is a collection of configuration and apps for a particular website.
A project can contain multiple apps.
An app can be in multiple projects.
```

To create an app, make sure you're in the same dirctory as manage.py and type this command:
```
$python manage.py startapp polls
```
The app's directory looks like this:
```
polls/
    __init__.py
    admin.py
    apps.py
    migrations/
	    __init__.py
    models.py
    tests.py
    views.py
```

## write your first view
Open the file polls/views.py and put the follwing Python code in it:
```
polls/view.py

from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, world. You're at the polls index.")
```

Create a URL map by create a URLconf in the polls directory, create a file called urls.py.

```
polls/urls.py

from django.urls import pth
from . import views
urlpatterns = [
    path('',views.index,name='index'),
]
```

The next step is to point the root URLconf at the polls.urls module. 
In mysite/urls.py, add an import for django.urls.include and insert an include() in the urlpatterns list, so you have:
```
mysite/urls.py

from django.urls import include, path
from django.contrib import admin

urlpatterns = [
    path('polls/', include('polls.urls')),
    path('admin/',admin.site.urls),
]
```
**When to use include()**
```
You should always use include() when you include other URLpatterns. admin.site.urls is the only exception to this.
```
Now run the server again
```
python manage.py runserver
```

# Writing the first Django app 
## Part II
## Database setup
By default, the configuration used SQLite. If youwish to use another database, install the appropriate database bindings and change the following keys in the DATABASES 'default' item to match your database connection settings:
```
mysite/settins.py

- ENGINE 
- NAME
- USER
- PASSWORD
- HOST
...
```

## Migrations
By default, INSTALLED_APPS section in the file of mysite/settings.py, conatins the follwing apps, all of which come with Django:
```
django.contrib.admin - The admin site. 
django.contrib.auth - An authentication system.
django.contrib.contenttypes - A framework for content types.
django.contrib.sessions - A session framwork.
django.contrib.messages - A messaging framework.
django.contrib.staticfiles - A framework for managing static files.
```
These applications are included by default as a convenience for the common case.

Some of these applications make use of at least one database table, though, so we need to create the tables in the database before we can use them.
To do that, run the folloing command:

```
$ python manage.py migrate
```
The migrage command looks at the INSTALLED_APPS settings in mysite/setting.py file and create any necessay database tables according to the database settings and the database migrations shipped with the app.

If you're interested, run the command-line client for your database and type \dt(PostgreSQL),... to display the tables Django created.

The default applications are included for the common case, but not everybody needs them. If you don't need any or all of them, feel free to comment-out them or delete appropriate line(s) from INSTALLED_APPS settings in the mysite/settings.py file before running migrate.


## Creating models
For polls app, we'll create two models: Question and Choice. 
```
polls/models.py

from django.db import models


class Question(models.Model):
    quesion_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')

class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)
```
## Activating models
Include the app in our project.
To include the app in our project, we need to add a reference to its configuration class in the INSTALLED_APPS setting. The PollsConfig class is in the polls/apps.py file, so its dotted path is 'polls.apps.PollsConfig'.

```
mysite/settings.py

INSTALLED_APPS = [
    'polls.apps.PollsConfig',
    'django.contrib.admin',
    ...,
]
```
After include polls.apps.PollsConfig to the settings.py, django knows to include the polls app. Let's run another command:
```
$ python manage.py makemigrations polls
```
By running makemigrations, you're telling Django that you've made some changes to your models (in this case , you've made new ones) and that you'd like the changes to be stored as a *migration*.

Migrations are how Django stores changes to your models (and thus your database schema) they are just files on disk. You can read the migration for your new model if you like; it's the file polls/migrations/0001_initial.py. Don't worry, you're not expected to read them every time Django makes one, but they're designed to be human-editable in case you want to manually tweak how Django changes things.

Let's see what SQL that migration would run. The *sqlmigrate* command takes migration names and returns their SQL:
```
$ python manage.py sqlmigrate polls 0001
```
You should see something similar to the following SQL sentences.
```
BEGIN;

CREATE TABLE "polls_choice"(
    "id" serial NOT NULL PRIMARY KEY,
    "choice_text" varchar(200) NOT NULL,
    "votes" integer NOT NULL
);
...
...
```

Now, run *migrate* again to create those model tables in your database:
```
$ python manage.py migrate
```
The migrate command takes all the migrations that haven't been applied (Django tracks which ones are applied using a special table in your database called django_migrations) and runs them against your database - essentially, synchronizing the changes you made to your models with the schema in the database.

as a sum, the three-step guide to making model changes:

- Change your models (in models.py)
- Run **python manage.py makemigrations** to create migrations for those changes
- Run **python manage.py migrate** to apply those changes to the database.


# Documentation
