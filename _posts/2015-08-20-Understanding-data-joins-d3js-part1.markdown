---
layout: post
title:  "Understanding Data Joins in D3.js - Part 1"
date:   2015-08-20 11:00:00
categories: blog javascript
tags: javascript d3.js d3 charts datajoins databinding
comments: true
---

D3.js stands for Data Driven Documents, so what does it exactly means? It simply means your document(visualization elements, dom elements) are controlled and driven by Data, so in nutshell elements correspond to data, change in data  should change element.

Mike Bostock has written a good blog, ["Thinking with Joins"](http://bost.ocks.org/mike/join/) about understanding and developing this thought process. At start it may seem to be a bit difficult to understand, as it occured to me but then I understood its philoshophy, here I will try to put the Data Joins in simple words the way I understood it.

If you ever had worked on Relational Database the most common thing that you learn is Table Joins, so what we do in table join is, join the data from two or more table based on some common table column values and gives you combined result.

If we think in similar way, when we want to plot some chart visualization or render some dom element which correspond to data, then we have to join the dom elements with data and output should be the dom elements corresponding to data.

Lets say we have three divs in our document - 

{% highlight html%}
<div></div>
<div></div>
<div></div>
{% endhighlight %}

And a array, *dataArray* of size three

{% highlight javascript%}
 var dataArray = ['one', 'two', 'three']
{% endhighlight %}

What we want is to render the data of array in div, one each. Will represent div and data in table format as


| Dom Index | Dom Element                 |
|-----------|-----------------------------|
| 1         | \<div> </div> (First div)   |
| 2         | \<div> </div> (Second div)  |
| 3         | \<div> </div> (Third div)   |

<br>

| Array Index | Array Element |
|-------------|---------------|
| 1           | 'one'         |
| 2           | 'two'         |
| 3           | 'three'       |


Now suppose if I want to join these, I can join them based on Index column in both tables as -

<h2 id="case1"><a>Case 1</a></h2>
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
      <td>&lt;div&gt; &lt;/div&gt; (First div)</td>
      <td>‘one’</td>
    </tr>
    <tr>
      <td>2</td>
      <td>&lt;div&gt; &lt;/div&gt; (Second div)</td>
      <td>‘two’</td>
    </tr>
    <tr>
      <td>3</td>
      <td>&lt;div&gt; &lt;/div&gt; (Third div)</td>
      <td>‘three’</td>
    </tr>
  </tbody>
</table>

Here all data element joined with div elements.
<br>

<h2 id="case2"><a>Case 2</a></h2>

What if array had four elements as ['one', 'two', 'three', 'four']
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
      <td>&lt;div&gt; &lt;/div&gt; (First div)</td>
      <td>‘one’</td>
    </tr>
    <tr>
      <td>2</td>
      <td>&lt;div&gt; &lt;/div&gt; (Second div)</td>
      <td>‘two’</td>
    </tr>
    <tr>
      <td>3</td>
      <td>&lt;div&gt; &lt;/div&gt; (Third div)</td>
      <td>‘three’</td>
    </tr>
    <tr>
      <td>4</td>
      <td class="highlightEmpty">&nbsp;</td>
      <td>‘four’</td>
    </tr>
  </tbody>
</table>
If you observe here first three divs gets corresponding 'Array Element' & 4th array element has no corresponding Dom element.

<h2 id="case3"><a>Case 3</a></h2>

And if array had only first two elements, ['one', 'two'] then there would be no corresponding data for third div as 

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
      <td>&lt;div&gt; &lt;/div&gt; (First div)</td>
      <td>‘one’</td>
    </tr>
    <tr>
      <td>2</td>
      <td>&lt;div&gt; &lt;/div&gt; (Second div)</td>
      <td>‘two’</td>
    </tr>
    <tr>
      <td>3</td>
      <td>&lt;div&gt; &lt;/div&gt; (Third div)</td>
      <td class="highlightEmpty">&nbsp;</td>
    </tr>
  </tbody>
</table>

<br>

We will use these cases later, now going back to our goal **render the data of array in div, one each** using d3.
d3 provides us three method corresponding to data join -

- [data()](https://github.com/mbostock/d3/wiki/Selections#data)
- [enter()](https://github.com/mbostock/d3/wiki/Selections#enter)
- [exit()](https://github.com/mbostock/d3/wiki/Selections#exit)

Lets use them -

First we want to bind data in array to div, so we need to select divs first, using d3.selectAll as -


{% highlight javascript%}
d3.selectAll('div')
{% endhighlight %}

and then bind the data - 

{% highlight javascript%}
var updateDivSelection = d3.selectAll('div').data(dataArray);
{% endhighlight %}

This call to data does few things - 

 - like we saw above, it joins the data with elements based on index

 - it returns the *selection of element (divs)* for which data has been joined, so in [Case 1]({{page.url}}#case1) we will get selection of 3 divs, in [Case 2]({{page.url}}#case2) we get selection of 3 divs & not 4, in [Case 3]({{page.url}}#case3) we get selection of 2 divs & not 3. These selections are called **Update** selection, and we captured it in variable *updateDivSelection*

 - if there are more number of elements in array passed compared to divs as in [Case 2]({{page.url}}#case2), it creates the placeholder div elements for these extra data elements, and you can access those using **enter()** call. This can be used to add new divs for the data element for which no div was available.

 - if there are less elements in array compared to divs as [Case 3]({{page.url}}#case3), then these extra div elements can be accessed using **exit()** call. And this can be used to remove the extra divs for which no data is available.

 So you now see how elements in dom can be driven/controlled as per data using above functions.

 Now lets try to put data from arrayData in divs for each case using above functions.

###Case 1 Implementation

{% highlight html%}
<div></div>
<div></div>
<div></div>
{% endhighlight %}

{% highlight javascript%}
var dataArray = ['one', 'two', 'three'];

var updateDivSelection = d3.selectAll('div').data(dataArray);

console.log(updateDivSelection[0].slice()); // [div, div, div]

updateDivSelection.html(function(d){return d;});

//enter should be empty
console.log(updateDivSelection.enter()[0].slice()); // []

//exit should be empty
console.log(updateDivSelection.exit()[0].slice()); // []

{% endhighlight %}

Above I logged update selection in console, you see all three divs there, then on next line I updated the inner html of these divs using html() function call.

In html() call we have to pass a function to which associated data 'd' will be passed and then you can perform any transformation on that data if you want and return value that need to be shown in inner html, here we simply retun data that came.

And what if I access enter() & exit() here, should be empty right ? Next lines does that and we get empty array. Working example -

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/qu59qzy0/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

You see the data in array has been put in div, the data passed can be used for any purpose of styling, position, size etc. using respective functions. Now you must have got a bit of idea how charts are rendered using data. Lets move to Case 2 -

**Note-** I have used slice() while logging array, I did that just to log array element and not object property along with it, else you may get confused whats in array and what are arrray object properties.

###Case 2 Implementation -

In this we should get 3 divs for update selection, 1 placeholder object in enter selection and none for exit selection. Working example - 

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/x8fkp4un/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

But here we did not used the enter to create new element, we will do it now, see it in action -

{% highlight javascript%}
updateDivSelection.enter().append('div').html(function(d){ return d;});
{% endhighlight %}

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/xq1r0Lt7/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

If you go through code you will see a small change I did while selecting divs, before d3.selectAll('div'), I selected 'body', this I did on purpose, so that newly appended div goes inside body and not in html element, you can read why we need to do this in [this article](http://bost.ocks.org/mike/selection/)

###Case 3 Implementation -

Moving on to Case 3 now, we will use *dataArray* of size two and then see how to remove extra div elements for which there is no corresponding (joined) data using exit()

Now suppose I initially put number 1, 2 & 3 in the divs as  -

{% highlight html %}
<body>
<div>1</div>
<div>2</div>
<div>3</div>
<body>
{% endhighlight %}

And then will simply update them with data that we pass in *dataArray*, code in action -

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/ye3vqp9b/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

You see above that third div (with data '3') remain as it is, but we want to remove it as it doesn't have corresponding data in *dataArray*, lets do that using exit(), as -

{% highlight javascript%}
updateDivSelection.exit().remove();
{% endhighlight %}

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/5zfd88ay/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

So this explains how data, enter and exit works, we will see some further details about key function and how to use object as data in [part 2]({% post_url 2015-08-20-Understanding-data-joins-d3js-part2 %}) of this article, hope this cleared basics of Data joins in d3.js.