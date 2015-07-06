---
layout: post
title: 'XKCD''s "Now" Coded in JavaScript'
redirect_from: /blog/post/xkcds-now-coded-in-javascript/
tags:
    - Guide
    - Playground

---
I love XKCD's web comics, they always give you a new way of looking at something and a laugh to boot. Yesterday at work I came accross the [Now](https://xkcd.com/1335/) comic. It shows you at a glance what countries are in business hours, or outside them. The image is rendered on the XKCD server and updated every hour when you refresh your page.

I wanted to re-code the comic so that it updates automatically, as the day goes on, in the browser. Below is the finished product:

<figure>
<div id="clock">
    <img id="map" src="https://diarmuid.ie/media/xkcd-clock/map.png" alt="Now" title="This image stays roughly in sync with the day (assuming the Earth continues spinning). Shortcut: xkcd.com/now" />
    <div id="clockTimeHour" class="clockTime"></div>
    <div id="clockTimeMinute" class="clockTime"></div>
    <div id="clockTimeSecond" class="clockTime"></div>
</div>
<script type="text/javascript">
setTimeout(function () { 
    updateMap(); // Initial rotation on load - give a bit of a delay so the user can see the startup animation
}, 500);

setInterval(function () {
    updateMap();
}, 1000); // Run check every second (for clock)

function updateElement (elementID, deg) {
    mapElement = document.getElementById(elementID);
    mapElement.style.webkitTransform = "rotate(" + deg + "deg)";
    mapElement.style.transform = "rotate(" + deg + "deg)";
    mapElement.style.MozTransform = "rotate(" + deg + "deg)";
}

function updateMap() {

    todayDate = new Date();
    todayDate.setHours(12, 0, 0, 0); // Time at 12 midday today

    elapsedSec = ((Date.now()) - todayDate) / 1000; // Seconds since midday

    UTCOffset = todayDate.getTimezoneOffset() * 60; // Calculate USC offset
    
    elapsedPerc = (elapsedSec + UTCOffset) / 86400; // Percentage of the day that has elapsed

    rotateDeg = Math.round(360 * elapsedPerc); // Convert percentage to degree
    
    
    elapsedPercHour = (elapsedSec) / 86400; // Percentage of the day that has elapsed
    rotateDegHour = Math.round(360 * elapsedPercHour); // Convert percentage to degree
    rotateDegMinute = Math.round(6 * ((new Date()).getMinutes())); // Convert percentage to degree
    rotateDegSecond = Math.round(6 * ((new Date()).getSeconds())); // Convert percentage to degree
    
    // Update Style with browser specific prefixes
    updateElement("map", rotateDeg);
    updateElement("clockTimeHour", rotateDegHour);
    updateElement("clockTimeMinute", rotateDegMinute);
    updateElement("clockTimeSecond", rotateDegSecond);
    
    if (rotateDegSecond == 0 && ((Math.random() + "").split(''))[2] == '0') {
        // randomly decide if the user gets a funky random slotlike spin
        updateElement("map", 360);
    }
}
</script>
<style>
#clock {
    background-image: url("https://diarmuid.ie/media/xkcd-clock/clock.png");
    width: 706px;
    height: 705px;
    overflow: hidden;
}
.clockTime {
    width: 4px;
    height: 600px;
    position: absolute;
    overflow: hidden;
    margin-top: -655px;
    z-index: 99;
    margin-left: 355px;
}
#clockTimeHour::after {
    margin-top: 80px;
    content: "";
    display: block;
    background-color: rgba(255, 255, 255, 0.3);
    width: 1px;
    height: 220px;
    border: .07em solid rgba(30, 30, 30, 0.6);
}
#clockTimeMinute::after {
    margin-top: 50px;
    content: "";
    display: block;
    background-color: rgba(255, 255, 255, 0.3);
    width: 1px;
    height: 250px;
    border: .07em solid rgba(30, 30, 30, 0.6);
}
#clockTimeSecond::after {
    margin-top: 0px;
    content: "";
    display: block;
    background-color: rgba(255, 255, 255, 0.3);
    width: 1px;
    height: 300px;
    border: .07em solid rgba(30, 30, 30, 0.6);
}
#clock,
#clock > img,
.clockTime {
    -webkit-transition: all cubic-bezier(.36,.65,.68,1) .8s;
        -moz-transition: all cubic-bezier(.36,.65,.68,1) .8s;
        -o-transition: all cubic-bezier(.36,.65,.68,1) .8s;
        transition: all cubic-bezier(.36,.65,.68,1) .8s;
}
</style>
<figcaption markdown="1">
Images by <a href="https://xkcd.com/1335/">XKCD.com</a>. Code by <a href="https://diarmuid.ie/blog/post/xkcds-now-coded-in-javascript">Diarmuid.ie</a>.
</figcaption>
</figure>

To automate the image I first broke it up into two parts:

- [map.png](/media/xkcd-clock/map.png) This is the part of the image that will rotate as the day goes on.
- [clock.png](/media/xkcd-clock/clock.png) This is the outer ring that remains stationary and shows the time in each country.

Using a bit of JS logic I calculated the percentage of the day that had passed since 12 midday, converted this into degrees and used the CSS3 rotate property (I've only tested this in Chrome and Firefox) to rotate the map image the calculated amount. The image will update every 30 seconds while you have this window open.

{% highlight javascript %}
setTimeout(function () { 
    updateMap(); // Initial rotation on load - give a bit of a delay so the user can see the startup animation
}, 500);

setInterval(function () {
    updateMap();
}, 1000); // Run check every second (for clock)

function updateElement (elementID, deg) {
    mapElement = document.getElementById(elementID);
    mapElement.style.webkitTransform = "rotate(" + deg + "deg)";
    mapElement.style.transform = "rotate(" + deg + "deg)";
    mapElement.style.MozTransform = "rotate(" + deg + "deg)";
}

function updateMap() {

    todayDate = new Date();
    todayDate.setHours(12, 0, 0, 0); // Time at 12 midday today

    elapsedSec = ((Date.now()) - todayDate) / 1000; // Seconds since midday

    UTCOffset = todayDate.getTimezoneOffset() * 60; // Calculate USC offset
    
    elapsedPerc = (elapsedSec + UTCOffset) / 86400; // Percentage of the day that has elapsed

    rotateDeg = Math.round(360 * elapsedPerc); // Convert percentage to degree
    
    
    elapsedPercHour = (elapsedSec) / 86400; // Percentage of the day that has elapsed
    rotateDegHour = Math.round(360 * elapsedPercHour); // Convert percentage to degree
    rotateDegMinute = Math.round(6 * ((new Date()).getMinutes())); // Convert percentage to degree
    rotateDegSecond = Math.round(6 * ((new Date()).getSeconds())); // Convert percentage to degree
    
    // Update Style with browser specific prefixes
    updateElement("map", rotateDeg);
    updateElement("clockTimeHour", rotateDegHour);
    updateElement("clockTimeMinute", rotateDegMinute);
    updateElement("clockTimeSecond", rotateDegSecond);
    
    if (rotateDegSecond == 0 && ((Math.random() + "").split(''))[2] == '0') {
        // randomly decide if the user gets a funky random slotlike spin
        updateElement("map", 360);
    }
}
{% endhighlight %}

You can play with the full source on [JSFiddle](http://jsfiddle.net/mrinc/tq819341/).
