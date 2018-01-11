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
