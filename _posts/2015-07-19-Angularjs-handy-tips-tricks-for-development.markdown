---
layout: post
title:  "Angular.js Handy Tips/Tricks for development"
date:   2015-07-19 11:57:49
categories: blog angularjs
tags: javascript angularjs
comments: true
---
Here are some handy tips that are useful when developing an Angular app.

<strong>1. Access the scope of inside the developers console -</strong>

To access the scope attached to element inside developers console -

Select the element (using chrome developers tool) for which you want scope and then use

{% highlight javascript %}angular.element($0).scope(){% endhighlight %}

Here "$0" is the selected element.
OR 
{% highlight javascript %} angular.element("#Element_Id").scope(){% endhighlight %}

e.g In angular phonecat app -

<img src="{{site.baseurl}}/images/scope.png" alt="Access Scope In Console" width="616" height="378" />

<br>
<strong> 2. Adding function or variable to scope from developer console -</strong>

Some time while development/debugging you may need to add/remove or tweak some property or function in scope, you can easily do that as -

Access the scope using above way
{% highlight javascript %}
var scope = angular.element("#Element_Id").scope()
scope.property_name = "new_value"
scope.new_function = function() {...}
{% endhighlight %}

But after executing this, you may observe that changes are not reflected on view make sure that you give call to -
{% highlight javascript %} scope.$apply();{% endhighlight %}

to reflect changes, or you can directly use -

{% highlight javascript %}
var scope = angular.element("#Element_Id").scope()
scope.$apply(function(){
scope.property_name = "new_value"
scope.new_function = function() {...}
})
{% endhighlight%}

<strong>3. Adding function from external javascript code/file to scope -</strong>

Some time you may have to make some changes to angular scope from outside code, you can use code mentioned above (Point 2.) to do that, just make sure that app is not in "$digest" or "$apply" cycle, if its in it you don't need to use $apply() , as -

{% highlight javascript %}
var scope = angular.element("#Element_Id").scope();
var phase = scope.$root.$$phase;
function scopeChanges(){
scope.property_name = "new_value"
scope.new_function = function() {...}
}

if(phase === $apply' || phase === '$digest'){
scopeChanges();
}
else{
scope.$apply(scopeChanges);
}
{% endhighlight %}

P.S - Will keep updating this post as I come across some more tricks/tips