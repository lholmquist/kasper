---
layout: post
title: "Story Creator - Part 3"
description: ""
tagline: "The Front End - Take One"
description: "The story behind the story creator and where it's going"
categories: [Aerogear, JBoss, Ideas, Personal]
tags: [javascript, bootstrap, java, Backbone, JBoss Forge, OpenShift]
---


[Previously on Blog]({{ BASE_PATH }}{{ page.previous.url }}), I made a few decisions on the backend and what the front end's styles would be.  Now all i had to do was figure out what JS framework i was going to use for the UI.

## Backbone.js

Figuring this part out was the hardest part.  There are so many frameworks out there, i wasn't sure what to choose.  One thing was for sure, I didn't want to use JSF( no hard feelings :) ).

The JDF guys had used Backbone.js for there ticket monster app that had just been released, so it gave me a nice starting point on how to get started with this framework.  There were good examples on how to keep the code modular.

I was able to get some things up and running pretty quickly.  The framework did a good job of making calls to my REST services pretty easily, hiding away the ugly bits.  And with databinding, my views were updated nicely.  The only downside was the amount of boilerplate code that i had to write.

## Reflection Time

So I had my front end,  I was connecting to my REST services, i was connecting to Facebook, sort of( just trust me ), all was good.  Although i had this, i wasn't really satisfied.  I felt like there was to much boilerplate code and i didn't really like the facebook integration.  Facebook does way to many requests for a mobile app.  Maybe the Facebook part wasn't all that important.  That kind of support could always be added later.  The game functionality really needed to be solid.

I also realized i had no security on my REST endpoints.  So anyone could spam them.  I wasn't storying any personal info,  but still.

That should bring us up to the present.  It was time to rewrite this thing, and make it better.  Most of the backend i liked, beside no security, but the front end would need some more work.

To Be Continued........[Go There Now]({{ BASE_PATH }}{{ page.next.url }})


![lolcat](http://www.clickuntilyoulaugh.com/wp-content/uploads/2012/02/funniest-lolcat-pics-uninterested.jpg)
