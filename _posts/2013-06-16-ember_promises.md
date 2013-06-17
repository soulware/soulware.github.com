---
layout: post
title: Promises In Ember
---

Asynchronous loading of data in Ember has many benefits but it can introduce race-conditions
if code is written with the assumption that data is available when it may still be loading.

Take a simple example like redirecting to a signin prompt unless the user is already authenticated.
How do you safely do this when currentUser may still be loading asynchronously?

    redirect: ->
      @transitionTo "signin" unless @get("currentUser")

If this is executed before currentUser has finished loading then the user will be immediately redirected,
regardless of data returned.

The when/then promise syntax can be used if currentUser is loaded with ember-data or directly through a jQuery ajax call -

    redirect: ->
      $.when(@get("currentUser")).then(
        (user) => @transitionTo "signin" unless user
      )

This is read as "_when_ the call to currentUser has successfully completed _then_ run the specified block of code
(in this case the conditional redirect).
This is literally a way to wait for an asynchronous call defined elsewhere to complete before checking the result.

Ember handlebar templates are promise aware by default. The following code will render correctly (and re-render automatically)
once currentUser has been loaded -

{% highlight html %}
{% raw %}
{{#if currentUser}}
  {{currentUser.name}}
{{else}}
  Guest
{{/if}}
{% endraw %}
{% endhighlight %}

Code in controllers or routes is not automatically promise aware;
code is not automatically rerun once a model is updated.

We cannot for example, guarantee the currentUser will be loaded when we attempt to print it directly in the console -

    user = @get("currentUser")
    console.log(user.get("name"))

The when/then syntax provides a clean way to wait for the data to be available before accessing it -

     $.when(@get("currentUser")).then(
       (user) -> console.log user.get("name")
     )

The when/then syntax can also be safely used even if the values are not promises.
The same syntax can be used to handle both synchronous and asynchronous data -

    x = true
    $.when(x).then(
      (result) -> console.log result
    )


References -

* [jQuery.when()](http://api.jquery.com/jQuery.when)
