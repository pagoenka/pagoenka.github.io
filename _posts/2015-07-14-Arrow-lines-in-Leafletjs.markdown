---
layout: post
title:  "Arrow lines in Leaflet.js"
date:   2015-07-14 11:57:49
categories: blog javascript
tags: javascript
---
Similar to my previous blog [Arrow Lines in Openlayers3]({% post_url 2015-07-13-Arrow-lines-in-Openlayers3 %}), I implemented the Arrow lines in <a href="http://leafletjs.com/">Leaflet.js</a>, another famous map library.

Leaflet by default don't provide rotation for the Icon on map, I used <a href="https://github.com/shramov/leaflet-plugins/blob/master/layer/Marker.Rotate.js">Marker.Rotate</a> plugin for it.

Compared to Openlayers example calculation of Arrow icon rotation are bit different and are done as per the Leaflet.js and Marker.Rotate plugin

The complete code can be found in <a href="http://jsfiddle.net/codefoolz/vyew8nom/" target="_blank">this fiddle</a>. Hope it helps!!

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/vyew8nom/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>