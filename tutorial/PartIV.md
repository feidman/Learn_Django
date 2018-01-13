# Learn Django
## Tutorial
### partI
### partII
### partIII
### partIV
- Key points #1
```
def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
	selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except ..
    ...
    else:
	selected_choice.votes += 1
	selected_choice.save()
	# Always return an HttpResponseRedirect after successfully dealing
	# with POST data. This prevents data from being posted twice if a 
	# user hits the Back button.
    return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```
(1) request.POST is a dictionary-like object that lets you access submitted data by key name.
In this case, request.POST['choice'] returns the ID of the selected choice, as a string. 
Please see the ‘POST data' from a form in the detail.html file.

request.POST['choice'] will raise KeyError if choice wasn't provided in POST data.

(2) As python comment above points out, you should alway return an HttpResponseRedirect after successfully dealing with POST data.
This tip isn't specific to Django; it's just good Web development practice.

Reverse() function helps avoid having to hardcode a URL in the view function.
It is given the name of the view that we want to pass control to and the variable portion of the URL pattern that points to that view.

(3) Note How to avoid Rase condition.
In our case, if two users try to vote at the exactly the same time, 
the votes might go wrong.

- Key poinst #2
    Use generic views : Less code is better
    Remaining for later review.
    ...

