---
layout: post
title:  "D3.js scales"
date:   2015-07-30 11:57:49
categories: blog javascript
tags: javascript d3.js d3
comments: true
---

In this post we will take look at scales in d3.js. How they work, how to use them, what are the type of scales and there corresponding usage.

So what does scale do?

Scale as it name says it scales, scales what? -> the input domain, to what -> the range?

Let talk in simple terms, you want to plot the any chart having some axes, or some mesurments by which you wish to represent data.

Now you want to display actual data in by plotting lines/bar/area on screen using pixels, now how to decide how much pixes should your inpur value should take

Say for example you want to plot (10,10),(20,20)...(100,100) (this called as domain) on the page using two dimentions like graphs, you have one container (div) with width of 100px by 100px

So whats big deal, consider Leftmost lowest point as (0,0) and Rightmost top point as (100,100)
<img src="" />

Thats pretty easy, but in reality you would not get such cases, suppose say now your domain consisting of higher values (1000,1000),(2000,2000).....(10000,10000), so now will you draw 10000px by 100000px, no right?

So for above domain if I say draw it in 100px by 100px container (div), what would be the best approach???

We can divide each value by 100 and then domain would be (10,10),(20,20)...(100,100) again but here each pixel will represent 100 not 10 as previously.

So you thought the area of container is same but the scale has been changed.

Ok so what we can divide domain and get it done, no?? 

No in practicle scenario your domain would not be such simple number nor the container width and height.

You may have domain starting from (230,150) i.e (x1,y1) to values upto (870,990) i.e (x2,y2) and container size may be of 170 px (width i.e x axis) by 470px (height i.e Y axis), depending on your design, so in these cases d3.scale help you.

Scale is calculated separatly for x and y, we will use linear scale here (type of scale explained later)

so for x - 

var xScale = d3.scale.linear();

Set the domain i.e input set of values (Give values in array)

xScale.domain([x1,x2]) i.e in our case xScale.domain([230,870]);

And the range for x axis 

xScale.range([start,end]) i.e xScale.range([0,170]); , 0 because we want to plot from 0 to further values.

Now the magic should happen :)

For input value of 230 I should get 0 on X-axis, lets see, try

xScale(230) // gives 0

And for input value 870 I should get 170, 

xScale(870) // Gives 170

Try any other value in between 230 to 870 and output should be between 0 to 170

Similarly for Y axis

var yScale = d3.scale.linear();
yScale.domain([150,990]);
yScale.range([0,470]);

Try

yScale(150) // gives 0

yScale(990) //gives 470

By this you get the point where to plot the points on given svg container