# Writing your first Django app, PartIII
- views
## Overview
A view is a "type" of Web page in your Django application that 
generally serves a specific function and has a specific template.
For example, in a blog app, you might have the folling views:
* Blog homepage
* Entry "detail" page
* Year-based archive page
* Month-base archive page
* Day-based archive page
* Comment action

For the polls app, we'll have the following pages:
* Question "index" page - displays the latest few questions
* Question "detail" page - displays the question text, with no results but with a form to vote.
* Question "results" page - displays results for a particular question.
* Vote action - handles voting for a particular choice in a particular question.

In Django, web pages and other content are delivered by views. 
**Each view is represented by a simple Python function (or method, in the case of class-based views).**
Django will choose a view by examining the URL that's requested (to be precise, the part of the URL after the domain name).

You will pleased to know that Django allows us much more elegant URL patterns than the normal ones, such as ".../dirmod.asp?sid=&type=gen&mod=...".

A URL pattern is simply the general form of a URL,
for example: /newsarchive/<year>/<month>/.

To get from a URL to a view, Django uses what are known as "URLconfs". 
A URLconf maps URL patterns to views.

## Writing more views
Now let's add a few more views to polls/views.py.
These views are slightly different, because they take an **argument**:
```
polls/views.py

def detail(request, question_id):
    return HttpResponse("You're looking at question %s." %question_id)

def result(request,question_id):
    response = "you're looking at the results of question %s."
    return HttpResponse(response % question_id)

def vote(request, question_id):
    return HttpResponse("you're voting on question %s." % question_id)
```
Wire these new views into the polls.urls module by adding the following path() calls:

```
polls/urls.py

from django.urls import path

from . import views

urlpatterns = [
    path('',views.index,name='index'),  
    path('<int:question_id>/',views.detail,name='detail'),  
    path('<int:question_id>/results/',views.results,name='results'),  
    path('<int:question_id>/vote/',views.vote,name='vote'),  
]
```
When somebody requests a page from you website - say,
"/polls/34/", Django will load the mysite.urls Python module
because it's pointed to by the ROOT_URLCONF setting.
It finds the variable named urlpatterns and traverses the patterns in order.
```
mysite/urls.py

URLpatterns = [
    #...
    path('polls/', include('polls/urls')),
]
```
After finding the match at 'polls/', 
it strips off the matching text("polls/") and sends the remaining text - 
"34/" to the 'polls/urls' URLconf for further processing.
There it matches '<int:question_id>/', resulting in a call to the detail() view like so:
```
detail(request=\<HttpRequest object\>, question_id=34)
```

The question_id=34 part comes from <int:question_id>.
Using angle brackets "captures" part of the URL and send it as a keyword argument to the view function.
The :question_id> part of the string defines the name that will be used to identify the matched pattern,
and the int: part is a converter that determines what patterns should match this part of the URL path.

## Write views that actually do something
Each view is responsible for doing two things: 
Returning an HttpResponse object containing the content for requested page,
or rasiung an exception such as Http404. 
The reset is up to you.

Your view can read records from a database, or not.
It can use a template system such as Django's -
or a third party template system - or not.
It can generate a PDF file, output XML, create a ZIP file on the fly, 
anything you want, using Python libraries you want.

All Django wants is that HttpResponse. Or an exception.

Let's use Django's own database API:
```
polls/views.py

from django.http import HttpResponse
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    output = ','.join([q.question_text for q in latest_question_list])
    return HttpResponse(output)

# Leave the rest of the views(detail, results, vote) unchanged
```
The problem is the page's design is hard-coded in the view.
If you want to change the way the page looks, you'll have to edit this Python code.
So, let's use Django's template system to seperate the design from Python by creating a template that the view can use.

Firt, create a subdirectory called templates in polls directory.
Within the templates dir, create another dir called polls, 
and within that create a file called index.html.
In other words, your template should be at /polls/templates/polls/index.html.

Put the following code in that template:

```
polls/templates/polls/index.html
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
	<li><a href="/polls/{{question.id}}/">{{ question.question_text}}</a></li>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

Now let's update index view
```
polls/views.py

from django.http import HttpResponse
from django.template import loader

from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    tempate = loader.get_template('polls/index.html')
    context = {
	'latest_question_list': latest_question_list,
    }
    return HttpResponse(template.render(context, request))
```
That code loads the template called polls/index.html and passes it a context.
The context is a dictionary mapping template variable names to Python objects.

Load the page by pointing your browser at "/polls/", and you should see a bulleted-list containing the "what's up" and "how are you" question. 
The link points to the question's detail page.

## A shortcut: render()
For replacing template.loader, and template.render
Rewitre the index function in polls/view.py file.
```
polls/views.py

from django.shortcuts import render
from .models import Question

def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {
	'latest_question_list': latest_question_list,
    }
    return render(request, 'polls/index.html', context) 

# remaining the other view functions unchanged
```
Note that once we've done this in all these views,
we no longer need to import loader and HttpResponse.
(you'll want to keep HttpResponse if you still have the stub methods for detail, results, and vote).

## Raising a 404 error
Now, let's tackle the question detail view - the page that displays the question text fro a given poll.
Here's th  view:
```
polls/views.py

from django.http import Http404
from django.shortcuts import render

from .models import Question
#...
def detail(request, question_id):
    try:
	question = Question.objects.get(pk=question_id)
    except Question.DoseNotExist:
	raise Http404("Question does not exist")
    return render(request,'polls/detail.html',{'question':question})
```
Create polls/templates/polls/detail.html file
```
polls/templates/polls/detail.html 


{{question}}
```

## A shortcut: get_object_or_404()
It's a very common idiom to use get() and raise Http404 if the object doesn't exist. Django provides a shortcut. Here's the detail() view. rewritten:
```
polls/views.py

from django.shortcuts import get_object_or_404, render

from .models import Question
#...
def detail(request, question_id):
    question = get_object_or_404(Question,pk=question_id)
    return render(request,'polls/detail.html',{'question':question})
```

## Use the template system
Back to the detail() view for our poll app.
Here's what the polls/detail.html template might look like:
```
polls/templates/polls/detail.html 

<h1>{{ question.question_text }}</h1>
<ul>
{% for choice in question.choice_set.all %}
    <li>{{ choice.choice_test }}</li>
{% endfor %}
</ul>
```
The template system uses dot-lookup syntax to access variable attributes.
See the [template guide](https://docs.djangoproject.com/en/2.0/topics/templates/) for more about templates.

## Removing hardcoded URLs in templates
Remember, when we wrote the link to a question in the polls/index.html template, 
the link was partially hardcode like this:
```
<li><a href="/polls/{{question.id}}/">{{ question.question_text}}</a></li>
```
The problem with this hardcoded,
tightly-coupled approach is that it becomes challenging to change URLs on projects with a lot of templates.
However, since you defined the name argument in the path() functions in the polls/urls module, you can remove a reliance on specific URL paths defined in your url configurations by using the {% url %} template tag:
```
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }} </a></li>
```

The way this works is by looking up the URL definition as specified in the polls.urls module. 
You can see exactly where the URL name of 'detail' is defined below:
```
...
# the 'name' value as called by the {% url %} template tag
path('<int:question_id>/',views.detail, name='detail'),
...
```

## Namespacing URL names
The tutorial project has just one app, polls.
However, in real Django projects, there might be five, ten, twenty or more.
How does Django differentiate the URL names between them? For example, the polls app has a detail view, and so might an app on the same project.
How does one make it so that Django knows which app view to create for a url when using the {% url %} template tag?

The answer is to add namespaces to your URLconf. 
In the polls/urls.py file, go ahead and add an app_name to set the application namespace:
```
polls/views.py

...
app_name = 'polls'
urlpatterns = [
path('', views.index, name='index'),
path('<int:question_id>/',views.detail, name='detail'),
path('<int:question_id>/results/',views.results, name='results'),
path('<int:question_id>/vote/',views.vote, name='vote'),
]
```
Now change your polls/index.html template from:
```
polls/tempaltes/polls/index.html
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }} </a></li>
```
to point at the namespced view:
```
<li><a href="{% url 'polls:detail' question.id %}">{{ question.question_text }} </a></li>
```
When you're comfortable with writing views, read part 4 of this tutorial to leearn about simple form processing and generic views.




