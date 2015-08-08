---
layout: post
title:  "D3.js scales"
date:   2015-07-30 11:57:49
categories: blog javascript
tags: javascript d3.js d3
comments: true
---

In this post we will take look at scales in d3.js. How they work, how to use them, what are the type of scales and there corresponding usage.

So what does scale do? Scale as it name says it scales, 

scales what? -> the input domain,

to what? -> the range

Let talk in simple terms, you have some collection of numbers, lets say even numbers from 0 to 10 i.e (0,2,4,6,8,10), this collection is known as domain and you want to plot these point on some line say having length of 360px, that should be easy, as line would start from 0 and length upto 360, so the range becomes (0,360) 

Like these

<svg height="50" width="450" margin="20"><g fill="none" stroke="black" shape-rendering="crispEdges" transform="translate(50,20)"><g class="tick" transform="translate(0,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" style="text-anchor: middle;fill:black;">0</text></g><g class="tick" transform="translate(72,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" style="text-anchor: middle;fill:black;">2</text></g><g class="tick" transform="translate(144,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" style="text-anchor: middle;fill:black;">4</text></g><g class="tick" transform="translate(216,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" style="text-anchor: middle;fill:black;">6</text></g><g class="tick" transform="translate(288,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" style="text-anchor: middle;fill:black;">8</text></g><g class="tick" transform="translate(360,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" style="text-anchor: middle;fill:black;">10</text></g><path class="domain" d="M0,6V0H360V6"></path></g></svg>

Here we have plotted 6 points(0,2,4,6,8,10) on the line of 360px in length,so that pixel distance of 60px is denoting 2 unit i.e 30 px for 1 unit. 

This points to pixel mapping was easy, this would not be the case always.
Say for example you want to plot (505, 999, 1311, 1750, 1893 ) on line of length 385px, now you are understanding the complexity, and this kind of data you would plot in real application, now how to decide how many pixels should be used to plot above domain, so here d3.scale comes handy.

The mapping of one set of numbers to other is known as Interpolation. D3 scales uses interplation of mapping data points to pixels, we will talk more about interpolation in another article. Beside this scale also offer few more things that you would need for plotting points, axes etc.

There are three types of scales - 

- Quantitative

- Ordinal 

- Time

Here we will talk about Linear scale which is of type Quantitative scale

Lets say we have following data set  { [3,7], [5,10], [10,20], [33,45], [67,98]	} ,
These point [x,y] can be plotted on two dimensional system i.e X-axis & Y-axis.
Scale should be calculated separatly for x and y axes.
And lets say X axis is of length 370px and Y axis is of 440px

Set of all x co-ordinates [3,5,10,33,67] & set of all y co-ordinates [7,10,20,45,98]

- So for x - 

{% highlight javascript %}

var xScale = d3.scale.linear();

{% endhighlight %}
Now set the domain i.e input set of values (Give array of lowest and highest value in set x)

{% highlight javascript %}

xScale.domain([x1,x2]) i.e in our case xScale.domain([3,67]);

{% endhighlight %}
And the range for x axis 

{% highlight javascript %}

xScale.range([start,end]) i.e xScale.range([0,370]);

{% endhighlight %}

Now the magic should happen :)

For input value of 3 I should get 0 on X-axis (lowest pixel value), lets see, try

{% highlight javascript %}
xScale(3) // gives 0
{% endhighlight %}

And for input value 67 I should get 370 (highest pixel value), 

{% highlight javascript %}
xScale(67) // Gives 370
{% endhighlight %}
Try any other value in between 3 to 67 and output should be between 0 to 370

<svg height="100" width="450" margin="20"><g fill="none" stroke="black" shape-rendering="crispEdges" transform="translate(50,20)"><g class="tick" transform="translate(0,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" fill="black" style="text-anchor: middle;">3</text></g><g class="tick" transform="translate(11.5625,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" fill="black" style="text-anchor: middle;">5</text></g><g class="tick" transform="translate(40.46875,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" fill="black" style="text-anchor: middle;">10</text></g><g class="tick" transform="translate(173.4375,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" style="text-anchor: middle;" fill="black">33</text></g><g class="tick" transform="translate(370,0)" style="opacity: 1;"><line y2="6" x2="0"></line><text dy=".71em" y="9" x="0" fill="black" style="text-anchor: middle;">67</text></g><path class="domain" d="M0,6V0H370V6"></path></g></svg>

Similarly for Y axis

{% highlight javascript %}
var yScale = d3.scale.linear();
yScale.domain([7,98]);
yScale.range([0,440]);
{% endhighlight %}

Try

{% highlight javascript %}
yScale(7) // gives 0

yScale(98) //gives 440
{% endhighlight %}

<svg height="500" width="150" margin="20"><g fill="none" stroke="black" shape-rendering="crispEdges" transform="translate(50,20)"><g class="tick" transform="translate(0,0)" style="opacity: 1;"><line x2="-4" y2="0"></line><text dy=".32em" x="-7" y="0" fill="black" style="text-anchor: end;">7</text></g><g class="tick" transform="translate(0,14.505494505494505)" style="opacity: 1;"><line x2="-4" y2="0"></line><text dy=".32em" x="-7" y="0" fill="black" style="text-anchor: end;">10</text></g><g class="tick" transform="translate(0,62.857142857142854)" style="opacity: 1;"><line x2="-4" y2="0"></line><text dy=".32em" x="-7" y="0" fill="black" style="text-anchor: end;">20</text></g><g class="tick" transform="translate(0,183.73626373626374)" style="opacity: 1;"><line x2="-4" y2="0"></line><text dy=".32em" x="-7" y="0" fill="black" style="text-anchor: end;">45</text></g><g class="tick" transform="translate(0,440)" style="opacity: 1;"><line x2="-4" y2="0"></line><text dy=".32em" x="-7" y="0" fill="black" style="text-anchor: end;">98</text></g><path class="domain" d="M-4,0H0V440H-4"></path></g></svg>

Ok I hope this makes the use of scale clear to you, but now how do we use it to plot the axes, and actual points and what are further usage of the Scale, that we will seee in next article.