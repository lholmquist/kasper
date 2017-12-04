---
layout: post
title:  "UnifiedPush, Promises and You"
date:   2016-06-07
permalink: unifiedpush-promises-and-you
categories: AeroGear Node.js UnifiedPush 
---

That's correct folks, it has finally happened.  The UnifiedPush Node Sender, https://www.npmjs.com/package/unifiedpush-node-sender , has been Promisified for the 0.13.0 release.

The API has changed slighty and it has also become a bit more "Functional" and now uses features from Node 4.x .

What does all this mean? 

It means this was a complete re-write of the code base and is **Not** a drop in replacement for the previous versions.

Lets take a look at an example to see how to use the new Sender.


First thing is to require the module:

    const unifiedPushSender = require('unifiedpush-node-sender');
    

The resulting `unifiedPushSender` is a function, so we can call it with some settings and it will return a Promise giving us the `client` object

	const settings = {
    	url: 'URL_TO_PUSH_SERVER',
        applicationId: 'APPLICATION_ID',
        masterSecert: 'SHHHH'
    }
    
    unifiedPushSender(settings).then((client) => {
    	client.sender.send(message, options).then(() => {
        });
    });


Now if we take a look at this client object, we see there is a `sender` object with 1 method called `send`

The `send` method is pretty identical to the previous version, except it now returns a Promise and no longer emits events or uses callbacks

**I'll say it again, No Event Emitter and No Callbacks, Only Zuul, ....... I mean Promises** 


And now i leave you with this cat picture:

![](http://i1.kym-cdn.com/photos/images/facebook/000/314/021/149.jpg)


