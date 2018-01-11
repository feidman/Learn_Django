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

## Play with the API
Let's play around with the free API Django gives you. 
To invoke the Python shell, use this command:
```
$python manage.py shell
```
we use this (python manage.py shell) instead of simply typing "python", 
because **manage.py** sets the DJANGO_SETTING_MODULE environment varialbe,
which gives Django the Python import path to your mysite/settings.py file.

Tying the database API
```
>>>from polls.models import Question,Choice
>>>Question.objects.all()
<QuerySet []>

# Create a new Question.
>>>from django.utils import timezone
>>>q = Question(question_text="What's your name?", pub_date=timezone.now())

# save the object into database
>>> q.save()

#Now it has an ID
>>>q.id
1
>>>q.question_text
"What's your name?

#Change values by change the attributes, then call save()

>>>q.question_text = "What's up?"
>>>q.save()
```

By now, <Question: Question object(1)> isn't a helpful representation of this object. 
Let's fix that by editing the Question model (in the polls/modes.py file)
and adding a **__str__()** method to both Question and Choice:

```
polls/models.py

from django.db import models

class Question(models.Model):
    #...
    def __str__(self):
	return self.question_text

class Choice(models.Model):
    #...
    def __str__(self):
	return self.choice_text
```
It's import to add **_str()__** methods to your models, 
not only for your own convenience when dealing with the interactive prompt,
but also because objects' representations
are used throughout Django's automatically-generated admin.

Now your try the Database API in Django's shell, make sure our **__str__()** addition was migrated to the database.
by simply typing: Python manage.py shell
```
>>>from polls.models import Question, Choice
>>>Question.objects.all()
<QuerySet [<Question: What's up>>]>
>>>Question.objects.filter(question_text__startswith='what')
...
...
```


## Introducing the Django Admin
### Creating an admin user
Run the following command to create a user who can login to the admin site.
```
$python manage.py createsuperuser
```
Enter your desired username, email address and password.
then start the development server and open a web browser and to to http://127.0.0.1:8000/admin/. 


### Make the poll app modifiable in the admin
The question is the poll's app does not displayed on the admin page.

Just one thing to do: we need to tell the admin that Question objects have an admin interface. 
To do this, open the polls/admin.py file, and edit it to look like this:
```
polls/admin.py

from django.contrib import admin

from .models import Question

admin.site.register(Question)
```
Login to admin site again, you will see the Questions,
and you can manipulate the questions.

