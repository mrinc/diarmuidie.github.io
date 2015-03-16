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
    <img id="XKCDmap" src="/media/xkcd-clock/map.png" alt="Now" title="This image stays roughly in sync with the day (assuming the Earth continues spinning). Shortcut: xkcd.com/now" />
</div>
<script type="text/javascript">
updateMap(); // initial rotation on load
setInterval(function () {
    updateMap();
}, 30000); // rotate every 30 sec
function updateMap() {
    todayDate = new Date();
    todayDate.setHours(12, 0, 0, 0); // Time at 12 midday today
    elapsedSec = (Date.now() - todayDate) / 1000; // Seconds since midday
    UTCOffset = todayDate.getTimezoneOffset() * 60; // Calculate USC offset
    elapsedPerc = (elapsedSec + UTCOffset) / 86400; // Percentage of the day that has elapsed
    rotateDeg = Math.round(360 * elapsedPerc); // Convert percentage to degree
    // Update Style with browser specific prefixes
    mapElement = document.getElementById("XKCDmap");
    mapElement.style.webkitTransform = "rotate(" + rotateDeg + "deg)";
    mapElement.style.transform = "rotate(" + rotateDeg + "deg)";
    mapElement.style.MozTransform = "rotate(" + rotateDeg + "deg)";
}
</script>
<style>#clock {background-image: url("/media/xkcd-clock/clock.png");width:706px;height:705px;overflow:hidden;}</style>
<figcaption markdown="1">
Images by <a href="https://xkcd.com/1335/">XKCD.com</a>". Code by <a href="https://diarmuid.ie/blog/post/xkcds-now-coded-in-javascript">Diarmuid.ie</a>.
</figcaption>
</figure>

To automate the image I first broke it up into two parts:

- [map.png](/media/xkcd-clock/map.png) This is the part of the image that will rotate as the day goes on.
- [clock.png](/media/xkcd-clock/clock.png) This is the outer ring that remains stationary and shows the time in each country.

Using a bit of JS logic I calculated the percentage of the day that had passed since 12 midday, converted this into degrees and used the CSS3 rotate property (I've only tested this in Chrome and Firefox) to rotated the map image the calculated amount. The image will update every 30 seconds while you have this window open.

    updateMap(); // Initial rotation on load

    setInterval(function () {
        updateMap();
    }, 30000); // Rotate every 30 sec

    function updateMap() {

        todayDate = new Date();
        todayDate.setHours(12, 0, 0, 0); // Time at 12 midday today

        elapsedSec = (Date.now() - todayDate) / 1000; // Seconds since midday

        UTCOffset = todayDate.getTimezoneOffset() * 60; // Calculate USC offset

        elapsedPerc = (elapsedSec + UTCOffset) / 86400; // Percentage of the day that has elapsed

        rotateDeg = Math.round(360 * elapsedPerc); // Convert percentage to degree

        // Update Style with browser specific prefixes
        mapElement = document.getElementById("map");
        mapElement.style.webkitTransform = "rotate(" + rotateDeg + "deg)";
        mapElement.style.transform = "rotate(" + rotateDeg + "deg)";
        mapElement.style.MozTransform = "rotate(" + rotateDeg + "deg)";

    }

You can play with the full source on [JSFiddle](http://jsfiddle.net/diarmuidie/wd9b8/).
