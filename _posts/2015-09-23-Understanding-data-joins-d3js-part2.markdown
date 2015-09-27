---
layout: post
title:  "Understanding Data Joins in D3.js - Part 2"
date:   2015-08-20 11:00:00
categories: blog javascript
tags: javascript d3.js d3 charts datajoins databinding
comments: true
---

So in my previous post [Understanding Data Joins in D3.js - Part 1]({% post_url 2015-08-20-Understanding-data-joins-d3js-part1 %})  we saw basics of Data joins, here we will see some more details but first a quick recap- 

**Quick recap -**

- use data() to assign data to elements, it returns the collection of element for which corresponding data was present, called as update selection
- by default data mapping i.e assigning data to element is done based on index of data and element
- enter() returns you collection of (placeholder) elements for which data was present but no element was found in dom, and this can be used to create new elements in dom using append()
- exit() return you collection of elements for which no corresponding data was present but element was there in dom, and this can be used to remove such elements using remove()
 

Keeping above things in mind consider now we have data as this -

{% highlight javascript%}
 var dataArray = [
	{value : 'one', color:'red', width: 100},
	{value: 'two', color:'green', width: 200},
	{value: 'three', color:'blue', width: 150},
	{value: 'four', color:'yellow', width: 300}
]
{% endhighlight %}

Each object in array has properties - **value, color and width**, we will use these to plot divs.
Now say initially we dont have any divs present in body -

{% highlight html%}
<!-- no div elements initially -->
<body>

</body>
{% endhighlight %}

And we want to div for each object in array, that should be easy right?, we know we can use enter() function for cases where we dont have corresponding elements and append those, lets do that -

{% highlight javascript%}
 function render(data) {
 	//First use data function and pass data
	var updateDivSelection = d3.select('body').selectAll('div').
		data(data);
	//But updateDivSelection will be empty

	//Use enter to append the divs
	var enterDivSelection = updateDivSelection.enter().
		append('div').
		text('div');
}

//Calling render function and passing the data
render(dataArray); 
{% endhighlight %}

Working example -

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/bry3gjsm/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This works as expected, but we haven't used the data from object for text and stylling div. Lets do that -  

{% highlight javascript%}
 function render(data) {
 	//First use data function and pass data
	var updateDivSelection = d3.select('body').selectAll('div').
		data(data);
	//But updateDivSelection will be empty

	//Use enter to append the divs
	var enterDivSelection = updateDivSelection.enter().
		append('div').
		text(function(d) { return d.value }).  //for inner text
		style({
			'background-color': function(d){ return d.color },
			'width': function(d){ return d.width + 'px' }
		}); //For stylling of div
}

//Calling render function and passing the data
render(dataArray); 
{% endhighlight %}

Here if you observe we have used [text()](https://github.com/mbostock/d3/wiki/Selections#text) and [style()](https://github.com/mbostock/d3/wiki/Selections#style) functions for text and stylling of divs, and to which we pass functions to extract value from object.

{% highlight javascript%}
	text(function(d) { return d.value })
{% endhighlight %}

{% highlight javascript%}
style({
	'background-color': function(d){ return d.color },
	'width': function(d){ return d.width + 'px' }
})
{% endhighlight %}

Here in **'d'** in function parameter is complete object corresponding to the element, and the we use that object to extract what we want from it.

{% highlight javascript%}
{ value: 'anyValue', color:'anyColor', width: anyWidth }
{% endhighlight%}

Lets see this in action - 

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/L8qqyjdg/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

**Note -** We could have combined data & enter statements like -

{% highlight javascript%}
 
	var updateDivSelection = d3.select('body').selectAll('div').
		data(data).
		enter().
		append('div').
		text(function(d) { return d.value }).
		style({
		'background-color': function(d){ return d.color },
		'width': function(d){ return d.width + 'px' }
		});
}

{% endhighlight %}

and still this would have worked but as a practice always keep the reference of returning value of data() and enter() like we did above using **'updateDivSelection'** and **'enterDivSelection**, we will see its advantage shortly.

Lets say we are using some real time data and our **dataArray** keeps updating after 1 sec and you want these changes should be reflected on the Dom, by update we mean either of these can happen - 

 - <h4 id="case1"><a>Case 1</a></h4> New object can be added to array
 - <h4 id="case2"><a>Case 2</a></h4> Object can be removed from array
 - <h4 id="case3"><a>Case 3</a></h4> For existing objects in array, only **width** can be updated

  Then how to reflect this changes on dom, for this there can be two approaches - 

 - Simple one would be to remove all the divs and re-render using new updated data
 - Execute update cycle (i.e change of width) only on existing data elements, execute append cycle for newly added data and remove cycle div for removed data. 

**First approach** is simple and straight forward, but its costly & not performace friendly if you have large amount of data and complex UI so should not be used.

**Second approach** a bit tricky to understand, and does not always guarantee to give you high performance in each case compared to first approach, this approach helps to optimise in case where you are just updating few properties of existing data object, like we are just updating *'width'* here 

But if you are changing each value or most of the values of object in data then this will similar as if you are rendering elements again and therby very similar to first approach. (Still only difference here will be you will using same divs again rather than creating new as in first approach) 

Or in case where you are removing almost all previous object and adding new object to array, then again it becomes almost similar to first approach, so you will not see a tremendous performace change in these cases.

But then how to implement **Second approach**??, remember **data()** gives us updated element collection, **enter()** gives us new element (placeholder) collection and **exit()** gives us removed element collection, by using these we can achieve that.

But now you will ask aren't we using it already? Yes we are, but we need to change our writing way a bit to achieve optimisation.

We will update dataArray after 1 sec and then will call **render** function again. Wait a second, calling render function again??? doesn't this sound like **First approach**, no we are not removing previous divs before calling render again. This is where beauty lies, d3 will execute update cycle on existing element only rather that removing and recreating all divs again.

Lets see how 

[Case 1]({{page.url}}#case1) -

Lets just add new object to dataArray and call the render again -

{% highlight javascript%}

 function render(data) {
 	//First use data function and pass data
	var updateDivSelection = d3.select('body').selectAll('div').
		data(data);
	//But updateDivSelection will be empty

	//Use enter to append the divs
	var enterDivSelection = updateDivSelection.enter().
		append('div').
		text(function(d) { return d.value }).  //for inner text
		style({
			'background-color': function(d){ return d.color },
			'width': function(d){ return d.width + 'px' }
		}); //For stylling of div
}

//Calling render function and passing the data
render(dataArray);

//Updating data after 1 sec and calling render again
setTimeout(function() {
	//push new object in dataArray
	dataArray.push({value: 'five', color:'grey', width: 350});
	//call render again
	render(dataArray);
},1000);
{% endhighlight %}

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/6397qork/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This was easy, we kept all of our previous code as it is, and 5th div was added. Re-run fiddle if you missed to see update.

[Case 2]({{page.url}}#case2) -

Lets just remove last object from dataArray and call the render again, but only this will be sufficient?? **no**, we haven't used **exit()** function to remove extra div, we will do it as -

{% highlight javascript%}

 function render(data) {
 	...
 	...

 	//Using exit to remove extra divs
 	updateDivSelection.exit().remove();
}

//Calling render function and passing the data
render(dataArray);

//Updating data after 1 sec and calling render again
setTimeout(function() {
	//remove last object from dataArray
	dataArray.pop();
	//call render again
	render(dataArray);
},1000);

{% endhighlight %}

Working example - 

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/kuw0aLgo/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This was also easy and simple, now lets move on to last case of updating width.

[Case 3]({{page.url}}#case3) -

Lets just update **width** of first object of dataArray and call render again, will this do what we want?? **no**, why because we are stylling div on enter() function not on data() function- 

{% highlight javascript%}
var enterDivSelection = updateDivSelection.enter().
		append('div').
		text(function(d) { return d.value }).  //for inner text
		style({
			'background-color': function(d){ return d.color },
			'width': function(d){ return d.width + 'px' }
		}); //For stylling of div

{% endhighlight %}		

so even if data() get some updates for div it will not reflect, what to do then? should we move style function call from *enter* to *data* ? then what will happen to newly added divs, they will not have *width* and *background-color*, **initially**, you can try it out. So should we have style() for both *enter* and *data* ?, lets just do that and see if it works -

{% highlight javascript%}

 function render(data) {
 	//First use data function and pass data
	var updateDivSelection = d3.select('body').selectAll('div').
		data(data).
		style({
			'background-color': function(d){ return d.color },
			'width': function(d){ return d.width + 'px' }
		}); //calling style on data as well

	//Use enter to append the divs
	var enterDivSelection = updateDivSelection.enter().
		append('div').
		text(function(d) { return d.value }).  //for inner text
		style({
			'background-color': function(d){ return d.color },
			'width': function(d){ return d.width + 'px' }
		}); //For stylling of div
}

//Calling render function and passing the data
render(dataArray);

//Updating data after 1 sec and calling render again
setTimeout(function() {
	//update first object width
	dataArray[0].width = '400';
	//call render again
	render(dataArray);
},1000);
{% endhighlight %}

Working example - 

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/Lewnusg0/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This works well, but we had to repeat same code twice, thats not good how to address that??

D3.js gives you one more way to deal with it, if you read [enter](https://github.com/mbostock/d3/wiki/Selections#enter) details, it reads -

**The enter selection merges into the update selection when you append or insert.**

That means after calling enter(), the data selection i.e *updateDivSelection* for us above will hold references for both updated and newly added div, and you can use this feature, so that you don't have to repeat code again, lets see how we do that -


{% highlight javascript%}

 function render(data) {
 	//First use data function and pass data
	var updateDivSelection = d3.select('body').selectAll('div').
		data(data);
	//Not calling style function here	

	//Use enter to append the divs
	var enterDivSelection = updateDivSelection.enter().
		append('div').
		text(function(d) { return d.value });  //for inner text
	//Not calling style function here as well

	//Use updateDivSelection now, which holds collection of both newly added
	// and updated divs and call style on it
	updateDivSelection.
	style({
			'background-color': function(d){ return d.color },
			'width': function(d){ return d.width + 'px' }
		});
}

		...
{% endhighlight %}

Working example - 

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/hb9gna15/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This gives us idea how we should write our code using d3.js, always use update selection
after **enter()** for properties which can change. And that's why I said earlier, its good to keep references of data() and enter() return values.

Now we will see last part of the data join series i.e **key functions**, now suppose say I remove **first** object of dataArray by using **shift()** function and will call render again, what ideally should happen is div with text 'one' should disappear, right? lets try that - 

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/3tv1ekp2/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

But here we see that instead of div with text 'one', div with text 'four' was removed, why so?
As we discussed in first part data join by default happens on basis of index, because of that, this thing is happening. Let dig in a bit - 

Initially there were no divs so join would be like -

<table>
  <thead>
    <tr>
      <th>Index</th>
      <th>Dom Element</th>
      <th>Array Element</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>(Placeholder First div)</td>
      <td>{value : 'one', color:'red', width: 100}</td>
    </tr>
    <tr>
      <td>2</td>
      <td>(Placeholder Second div)</td>
      <td>{value: 'two', color:'green', width: 200},</td>
    </tr>
    <tr>
      <td>3</td>
      <td>(Placeholder Third div)</td>
      <td>{value: 'three', color:'blue', width: 150},</td>
    </tr>
    <tr>
      <td>4</td>
      <td>(Placeholder Fourth div)</td>
      <td>{value: 'four', color:'yellow', width: 300}</td>
    </tr>
  </tbody>
</table>

After we append the divs using **enter()** then join would be on index based and will look like

<table>
  <thead>
    <tr>
      <th>Index</th>
      <th>Dom Element</th>
      <th>Array Element</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td> &lt;div&gt; one &lt;/div&gt;</td>
      <td>{value : 'one', color:'red', width: 100}</td>
    </tr>
    <tr>
      <td>2</td>
      <td> &lt;div&gt; two &lt;/div&gt;</td>
      <td>{value: 'two', color:'green', width: 200}</td>
    </tr>
    <tr>
      <td>3</td>
      <td> &lt;div&gt; three &lt;/div&gt;</td>
      <td>{value: 'three', color:'blue', width: 150}</td>
    </tr>
    <tr>
      <td>4</td>
      <td> &lt;div&gt; four &lt;/div&gt;</td>
      <td>{value: 'four', color:'yellow', width: 300}</td>
    </tr>
  </tbody>
</table>

Then after 1 sec. we remove first object from dataArray using shift and it looks like -

{% highlight javascript%}
 var dataArray = [
	{value: 'two', color:'green', width: 200},
	{value: 'three', color:'blue', width: 150},
	{value: 'four', color:'yellow', width: 300}
]
{% endhighlight %}

After calling render again data would be joined again, based on index and will look like - 

<table>
  <thead>
    <tr>
      <th>Index</th>
      <th>Dom Element</th>
      <th>Array Element</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td> &lt;div&gt; one &lt;/div&gt;</td>
      <td>{value: 'two', color:'green', width: 200}</td>
    </tr>
    <tr>
      <td>2</td>
      <td> &lt;div&gt; two &lt;/div&gt;</td>
      <td>{value: 'three', color:'blue', width: 150}</td>
    </tr>
    <tr>
      <td>3</td>
      <td> &lt;div&gt; three &lt;/div&gt;</td>
      <td>{value: 'four', color:'yellow', width: 300}</td>
    </tr>
    <tr>
      <td>4</td>
      <td> &lt;div&gt; four &lt;/div&gt;</td>
      <td class="highlightEmpty"></td>
    </tr>
  </tbody>
</table>


So upadate selection would of size 3 and enter selection of size 0, and after enter selection we are just updating width and color, no text change we are doing, that why text remains same, and exit selection of size 1 and fourth div gets removed rather than first.

Then how to fix this??, this all is happening only beacause of join is happening based on index rather than some other key, to do that we use **Key Function** as- 

{% highlight javascript%}

 function render(data) {
 	//First use data function and pass data

 	//Note here using key function in data() call
	var updateDivSelection = d3.select('body').selectAll('div').
		data(data, function(d) { return d.value}); //Passing key function
	
	...
{% endhighlight%}

Here we are using **value** as joining constraint, so that whenever next time join happens it should be based on *value*, if only *value* matches then only join else don't, this is very similar to one-to-one table join in relational DB based on some constraint (join table_one with table_two where table_one.key1 == table_two.key2).

So after this, join would look like -

<table>
  <thead>
    <tr>
      <th>Index</th>
      <th>Dom Element</th>
      <th>Array Element</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td> &lt;div&gt; one &lt;/div&gt;</td>
      <td class='highlightEmpty'></td>
    </tr>
    <tr>
      <td>2</td>
      <td> &lt;div&gt; two &lt;/div&gt;</td>
      <td>{value: 'two', color:'green', width: 200}</td>
    </tr>
    <tr>
      <td>3</td>
      <td> &lt;div&gt; three &lt;/div&gt;</td>
      <td>{value: 'three', color:'blue', width: 150}</td>
    </tr>
    <tr>
      <td>4</td>
      <td> &lt;div&gt; four &lt;/div&gt;</td>
      <td>{value: 'four', color:'yellow', width: 300}</td>
    </tr>
  </tbody>
</table>

Testing this out -

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/2cLpoz39/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

So this works as expected, in practice if you have data that always updates use key function to avoid such things.

Now you must have got idea how to write chart which gets real time data and you need to reflect it on chart.

This is all for **Data Joins**  in **d3.js**, hope it helped you to understand it.