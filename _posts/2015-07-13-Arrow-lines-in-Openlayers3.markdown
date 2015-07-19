---
layout: post
title:  "Arrow lines in Openlayers 3"
date:   2015-07-13 11:57:49
categories: blog javascript
tags: javascript
comments: true
---
Recently working on map using <a title="Openalayers 3" href="http://openlayers.org/">Openlayers 3</a> I found there is no direct way to draw the Arrow lines using the <a title="Openalayers 3" href="http://openlayers.org/">Openlayers 3</a> , so what I did, used LineString and Icon to draw an Arrow.

I was able to draw the line and put icon at the end of line but next problem was how to rotate the Arrow image so that direction Arrow Line looks proper. So to find the angle to rotate arrow image you need to calculate -
<ol>
	<li>Slope of the line</li>
	<li>The quadrant in which line reside with respect to map co-ordinate system</li>
</ol>
First render map as -

{% highlight javascript %}
	var tileLayer = new ol.layer.Tile({source: new ol.source.OSM()});
var map = new ol.Map({
layers: [tileLayer],
view: new ol.View({center: ol.proj.transform([77.444504,22.795357], 'EPSG:4326','EPSG:3857'),zoom: 4}),
target: 'mapDiv'
});
{% endhighlight %}

Now suppose say we want to draw line from Source point (Using Cities in India) Nagpur (79.0900, 21.1500) to Mumbai(72.776714, 18.948932), New Delhi(77.2090,28.6139), Patna(85.1000,25.6000) and Hyderabad(78.4800,17.3700)

We declare a array called 'coordinates' and transform these into EPSG:3857 by iterating over 'coordinates' and store into' transformedCoordinates'

{% highlight javascript %} 
var coordinates = [[79.0900, 21.1500],[72.776714, 18.948932],[77.2090,28.6139],[85.1000,25.6000],[78.4800,17.3700]];
var transformedCoordinates = new Array();
{% endhighlight %}

And then we render cluster of the coordinates for each city. Now we need to draw line from Nagpur city to other this is done by using LineString
{% highlight javascript %}
var featureLine = new ol.Feature({
geometry: new ol.geom.LineString(lineArray)
});
{% endhighlight %}

Now most important part come drawing arrow heads, we can draw any icon using  '<strong>ol.style.Icon</strong>' but we need to rotate it so that its direction is proper for this we first calculate the angle of inclination of the line between city using -

{% highlight javascript %}
	var firstPoint = coordinates[0];
var secondPoint = coordinates[i];
var slope = ((secondPoint[1] - firstPoint[1]) / (secondPoint[0] - firstPoint[0]));
var angle = Math.atan(slope);
{% endhighlight %}

Now we need to determine the quadrant in which these line reside with respect to Origin point (Nagpur), we first shift the origin of the map to Nagpur as -

{% highlight javascript %} 
secondPoint[0] = secondPoint[0] - firstPoint[0];
secondPoint[1] = secondPoint[1] - firstPoint[1];
{% endhighlight %}

And then decide the rotation for our image as -

{% highlight javascript %}
//Fourth quadrant
if (secondPoint[0] > 0 && secondPoint[1] < 0) {
rotation = (Math.PI / 2 - angle);
}
//Second quadrant
else if (secondPoint[0] < 0 && secondPoint[1] > 0) {
rotation = -(Math.PI / 2 + angle);
}
//Third quadrant
else if (secondPoint[0] < 0 && secondPoint[1] < 0) { rotation = 3 * Math.PI / 2 - angle; } 
//First quadrant 
else if (secondPoint[0] > 0 && secondPoint[1] > 0) {
rotation = angle;
}
{% endhighlight %}

The rotation angle calculation depends the image that you are using, here I am using vertical arrow head (^) , if you are using some other image do corresponding changes in Quadrant condition above.

And we are done now simply use this rotation angle for the arrow head and add layer to map.

The complete code can be found in <a href="http://jsfiddle.net/codefoolz/m3q16vny/" target="_blank">this fiddle</a>. Hope it helps!!

<iframe width="100%" height="300" src="//jsfiddle.net/codefoolz/m3q16vny/embedded/" allowfullscreen="allowfullscreen" frameborder="0"></iframe>