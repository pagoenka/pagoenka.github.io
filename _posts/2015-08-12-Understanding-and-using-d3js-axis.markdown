---
layout: post
title:  "Understanding & using D3.js axis"
date:   2015-08-12 11:57:49
categories: blog javascript
tags: javascript d3.js d3 charts axis
comments: true
---

In previous [article]({% post_url 2015-07-30-Understanding-d3js-scales %}) we saw how d3 scales help us to map data to pixels. In this post we will how to use those for plotting axes.

Let say we have following set of data that we need to show these on chart, say a small circle for each point (just like graph) on for given point in data -

{% highlight javascript %}
var data = [
	[5,2],
	[15,20],
	[18,24],
	[28,32],
	[35,40],
	[58,69]
]
{% endhighlight %}

First we need X-axis and Y-axis, as said in earlier post we can use scales for it, lets do that

Lets say we have svg of width of 600px and height of 550px

````
<svg width="600" height="550"></svg>
````

So now domain and range for both axes becomes -

**For X-axis-** Domain = (5,58) Range = (0,600)

**For Y-axis-** Domain = (2,69) Range = (0,550)

Finding domain manually is not right way to do, d3.js provides you utility called [extent](https://github.com/mbostock/d3/wiki/Arrays#d3_extent) to get smallest and largest element for given array input, its syntax -

````
d3.extent(array[, accessor])
````

Accessor is function which says how to take value from which we need to find smallest and largest number.

````
d3.extent([1,2,3,4]) //Without accessor returns [1,4]
````
But for our data we would use it like

**Get X domain -**

````
d3.extent(data, function(d) { return d[0]}); //Returns [5,58]
````

**Get Y domain -**

````
d3.extent(data, function(d) { return d[1]}); //Returns [2,69]
````

Lets create scale then - 

**For X-axis-**

{% highlight javascript %}
 var xScale = d3.scale.linear()
 	.domain(d3.extent(data, function(d) { return d[0]}))
 	.range([0, 600]); //The width
{% endhighlight %}

**For Y-axis-**

{% highlight javascript %}
 var yScale = d3.scale.linear()
 	.domain(d3.extent(data, function(d) { return d[1]}))
 	.range([0, 550]); //The height, here we are doing a small mistake that we will correct later
{% endhighlight %}

Now plot actual axis using axis utility of d3 as -

{% highlight javascript %}
//Create x- axis
var xAxis = d3.svg.axis()
                  .scale(xScale) //pass scale 
                  .orient("bottom"); //orient axis horizontal
                  
//Plot axis as
d3.select("svg")
	.append("g")
	.call(xAxis); //Pass created axis
{% endhighlight %}

and

{% highlight javascript %}
//Create Y-axis
var yAxis = d3.svg.axis()
                  .scale(yScale) //pass scale 
                  .orient("right"); //orient axis verticle
                  
//Plot axis as
d3.select("svg")
	.append("g")
	.call(yAxis); //Pass created axis
{% endhighlight %}

Complete code and output - 

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/cmtqnwgq/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

But here you may observed few things -

 - Y-axis tick are on right side, should be on left of axis line for better visual
 - Origin point (0,0) is at top-left corner i.e Y-axis ticks are large to small from bottom to top on axis, generally we have small to large
 - X-axis is on top, generally we plot it at bottom
 - The line of axes should be thin and ticks should be small line to indicate their position
 
 

Let's see how to fix these, first if you see code we have use orient on axis, so what it does?, it very easy to understand, read it [here](https://github.com/mbostock/d3/wiki/SVG-Axes#orient).

So for fixing Y-axis tick problem we can simply use orient('left'), right? Let's try it out -

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/r8yLn8mg/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

**Voila !!!** where has Y-axis gone?? It still there but not visible to use, because its rendered outside of the boundries of our svg, so how to fix it, **simply shift it to right** say by 50px, but how? adding some padding to SVG say 50px, but adding padding would reduce the plotting area if we keep the width and height same, so to avoid that we will add padding to width & height and then set it to svg -

{% highlight html%}
<svg></svg>
{% endhighlight%}

{% highlight javascript%}
var height = 550,
	width =  600,
	padding = 50;

d3.select('svg').attr('width', width + 2 * padding) // 2 * padding because of left & right padding
				.attr('height', height + 2 * padding ). //similarly top & bottom padding
				style('padding', 50);
{% endhighlight%}

Rest all remains same, only small change I did is usign height & width for setting range rather than actual numbers -

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/dvdjyk1c/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Moving on to second issue of origin point i.e tick values on Y-axis, are large to small from bottom to top, however we need its reverse, i.e smallest value should be on bottom of axis and large at top, so that origin point would be at bootom-left so how to fix it?

If you remember when we plotted Y-axis first time, I mentioned in comment, that we are doing a small mistake here- 

{% highlight javascript %}
 var yScale = d3.scale.linear()
 	.domain(d3.extent(data, function(d) { return d[1]}))
 	.range([0, 550]); //The height, here we are doing a small mistake that we will correct later
{% endhighlight %}

Actually the co-ordinate system of SVG has it origin point (0,0) on **TOP-LEFT** corner so whatever tou plot in svg in terms of X & Y are with respect to that point, but we want out origin point (0,0) at bottom left, so how to change that by doing a very small change in range of Y scale as  - 

{% highlight javascript %}
 var yScale = d3.scale.linear()
 	.domain(d3.extent(data, function(d) { return d[1]}))
 	.range([height, 0]); //Changed the order
{% endhighlight %}

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/tf03jh7u/embedded/result,js,html,css" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

This made origin point to bottom-left and Y-axis tick are now small to large upwards.
But still X-axis is on top, now to bring it down we need to shift it to bottom by [Translating](https://github.com/mbostock/d3/wiki/Math#d3_transform) vertically as -

{% highlight javascript %}
                  
//Plot axis as
d3.select("svg")
	.append("g")
	.call(xAxis) //Pass created axis
	.attr("transform", "translate(0,"+ height +")"); //Translate by height

{% endhighlight %}

<iframe width="100%" height="700" src="//jsfiddle.net/codefoolz/h4xrpqm4/embedded/result,js,html,css/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Hurrey!!! We have the both axes in place and the way we want it.

Now we'll solve the last issue of making thin axes and visible tick lines, to do that you just need to define & apply some css as -

{% highlight css%}

.axis text {
  font: 10px sans-serif;
}

.axis path,
.axis line {
  fill: none;
  stroke: black;
  shape-rendering: crispEdges;
}

.axis path{
    stroke-width: 2;
}
{% endhighlight %}

Here we defined a class named axis and its relative style, we will add this class to the axis as  -

{% highlight javascript %}
                  
//X axis
d3.select("svg")
	.append("g")
	.call(xAxis) //Pass created axis
	.attr('class', 'axis')
	.attr("transform", "translate(0,"+ height +")"); //Translate by height

//Y axis
d3.select("svg")
	.append("g")
	.attr('class', 'axis')
	.call(yAxis); //Pass created axis


{% endhighlight %}

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/zczaq9h3/embedded/result,js,css,html" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

Looks good now!! Further you can control various aspect of axis such as number of ticks, format of ticks etc. for reference go through [docs](https://github.com/mbostock/d3/wiki/SVG-Axes)

Hope this clarified how to plot and use axis in d3.js, we will plot actual points in next article.