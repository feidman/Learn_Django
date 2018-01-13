# Writing your first Django app, part 6
We'll now add a stylesheet and an image.
## Customize your app's lok and feel

Put the following code in that stylesheet (polls/static/polls/style.css):
```
polls/static/polls/style.css
li a {
    color: green;
}
```
Next, add the following at the top of polls/templates/polls/index.html:
```
polls/templates/polls/index.html
{% load static %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
```
## Adding a background-image
put your image in polls/static/polls/images/background.gif.
Then, add to your stylesheet (polls/static/polls/style.css):
```
polls/static/polls/style.css
body {
    background: white url("images/background.gif") no-repeat right bottom;
}
```

These are the basics. For more details on settings and other bits included with the framework see the static files howto and the staticfiles reference. Deploying static files discusses how to use static files on a real server.


