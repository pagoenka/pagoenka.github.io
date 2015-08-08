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

Or if you are using jQuery, you can use the normal element selector

{% highlight javascript %}
 angular.element("#Element_Id").scope()
{% endhighlight %}

e.g In angular phonecat app -

<img src="{{site.baseurl}}/images/scope.png" alt="Access Scope In Console" width="616" height="378" />

If you are not using jQuery then you will get error *"Looking up elements via selectors is not supported by jqLite"* then you can use normal javascript way like this instead

{% highlight javascript %}
 angular.element(document.getElementById('element_id')).scope()
{% endhighlight %}

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

<strong>4. Getting all the controllers,directives,services,filters etc. for your App in console -</strong>

Sometimes you may want to check what all services/controllers/filters are registered in App.
(In case where controllers/services are dynamically loaded). To do that first get referencce to module where you have registered these things.

e.g If you have app named *myApp* and you have registered controller on it as

{% highlight javascript%}
	var app = angular.module('myApp',[]);
	// Adding controller
	app.controller('MyController', [function(){ ... }])
	app.controller('OtherController', [function(){ ... }])

	//Adding service/factory
	app.factory('myFactory', [function(){...}]);
	app.service('myService', [function(){...}]);

	//Adding filter
	app.filter('myFilter', [function(){...}]);

	//Adding directive
	app.directive('myDirective', [function()]){...}

	//Adding value
	app.constant('myValue','value');
{% endhighlight%}

Then you can check if indeed these are registered properly in console by accessing _invokeQueue

{% highlight javascript%}
	var appRef = angular.module('myApp'); // Get reference to module
	// Note called module without passing []
	var arr = appRef._invokeQueue
{% endhighlight%}

This _invokeQueue give you an array of object (array) of all registered services, controllers etc. Now you can iterate over this array get the name as - 

{% highlight javascript%}
// In continuation with above
console.log(arr[0][2][0]) // MyController
console.log(arr[1][2][0]) // OtherController
console.log(arr[2][2][0]) // myFactory
console.log(arr[3][2][0]) // myService
console.log(arr[4][2][0]) // myFilter
console.log(arr[5][2][0]) // myDirective
console.log(arr[6][2][0]) // myValue

//Get by which provider was used to register controller, service etc.
console.log(arr[0][0]) // $controllerProvider
console.log(arr[1][0]) // $controllerProvider
console.log(arr[2][0]) // $provide
console.log(arr[3][0]) // $provide
console.log(arr[4][0]) // $filterProvider
console.log(arr[5][0]) // $compileProvider
console.log(arr[6][0]) // $provide
{% endhighlight %}



P.S - Will keep updating this post as I come across some more tricks/tips