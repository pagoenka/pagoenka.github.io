---
layout: post
title:  "Interpolation"
date:   2015-07-30 12:57:49
categories: blog javascript
tags: javascript d3.js d3
comments: true
---

D3 scales use interpolation undeline, so what is interpolation, it is an function that maps one range to another (domain).

What does that mean suppose there is domain (d1,d2) and you want to express this domain in the range of (r1,r2)


d1............................d2


r1....................................................r2



There is a point d in domain (d1,d2)

d1.............d..............d2


You want to express this point in range (r1,r2)

One thing is clear that d1 corresponds to r1 and d2 to r1,r2

Now suppose say point r corresponds to d and shown as

r1.......................r............................r2

Then this should hold true

d - d1 / d2 - d1 = r - r1/ r2 - r1 


  <---------(d2 - d1)-------->
  <--(d - d1)-->
d1.............d..............d2


  <-------------------(r2 - r1)---------------------->
  <------(r - r1)------->
r1.......................r............................r2

Solving above equation for r gives us

r = ((d - d1) / (d2 - d1)) * (r2 - r1) + r1


if d1 = 0 and d2 = 1 then

r = d * (r2 - r1) + r1