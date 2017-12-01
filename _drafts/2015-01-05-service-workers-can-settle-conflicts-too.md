---
layout: post
title:  "Service Workers can settle conflicts, too"
date:   2015-01-05
permalink: service-workers-can-settle-conflicts-too
categories: 
---

Recently, i put my concerns about a Conflict Resolution library as part of AeroGear.js in a Mailing list thread.

http://lists.jboss.org/pipermail/aerogear-dev/2014-December/010296.html

The basic gist is that in Javascript, each framework/Library has their own way of doing requests to the server and adding in yet another way of doing it,  just to handle conflict resolution seemed silly.


Our current Conflict Resolution "spec" basically states that the server needs to send back a 409 status code along with the most up-to-date data if there is a conflict.  

And the client should be able to "auto-merge" the conflict/Handle the conflict in a "callback"/or just error.

I've been looking at and playing with Service Workers a bunch lately,  and started to think about possibly using them to integrate our conflict resolution stuff into existing frameworks and libraries.


## Service Worker

For those of you who don't know what a Service Worker is,  check this out,  http://jakearchibald.com/2014/using-serviceworker-today/

I'l wait......

Ok.

One high level concept is that we are now able to program the network layer. which means we can modify the response we send back to the user


This got me thinking.


## Programming the Network Layer


Since we now can access the Response of the request before our "app" gets it, We can perhaps run this conflict resolution code here.

** note: much of this code will be some what pseudo code **

So in our Service Worker file,  we will listen to the `fetch` event.

	self.addEventListener('fetch', function (event) {
    
    	if (event.request.url === 'THE URL FOR PUTting DATA') {
        	// We need to clone the Request since you are only 					allowed to 'Read' the body once
        var request = event.request.clone();
        
        
		// Now we are handling the request to the server
        event.respondWith(
        	fetch(event.request).then(function (response) {
            	// Also clone the response
                var r = response.clone();
                
                // Check the status
                if (r.status !== 409) {
                	return response;
                }
				
                // There is a conflict,  so do the magic
                var magicDiff = coolFunctionToDoDiffMagic();
                
                // Now that have the diff, we can either auto-merge or send back the conflict status with the "massaged" response
                
                return new Response(/* do crazy response creation here */)
                
                
            });
        )
        }
    });


I left out much of the exact code since i'm trying to get a concept across.


What this does though is allow us to integrate with existing libraries without adding yet another 'Ajax' library


## Some Cons

While this is a cool concept, there is however some things that can cause an issue.

The first, and probably most important is current implemenation status of Service Workers.

As of this writing,  Service Workers are in Chrome Beta, and Firefox dev edition.  So many things are still in flux.

The other issue here is that Service workers don't start to 'listen' for fetch events until after you install and reload the page.


But considering this is a POC, i think this idea is really cool.


To check out the code for this POC, check out this repo:  https://github.com/lholmquist/conflict-resolution-service-workers-demo

Please note that it is in flux and there is really no documentation here.


i think basically, you can do a 

	npm install
    
    bower install
    
    node app.js
    
    
Then running Chrome Canary or Firefox Developer Edition,  open the page.

I would use Chrome Canary since there is a Service Worker Inspector Dev tool thingy



![](http://www.wmscnet.com/disneypublic.jpg)

