---
layout: post
title:  "Promise Rejection Handling"
date:   2016-11-01
permalink: promise-rejection-handling
categories: Node.js Promises 
---

Node.js 7.0.0 was released last week.  One cool thing that was added, which might also freak some people out, is this: 

	 DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.

While `UnhandledPromiseRejectionWarning` has been in node since [6.6.0](https://github.com/nodejs/node/blob/master/doc/changelogs/CHANGELOG_V6.md#2016-09-14-version-660-current-fishrock123), this deprecation warning is new

     
This basically means, you've rejected a promise in your code, but you are not handling it, and pretty soon that will "**fuck your shit up**".  I'm paraphrasing, of course.


So lets see a very simple example of how to trigger this:

	Promise.reject();
    
    
If we run this in node 6.5 and below we might get something like this:

	> Promise.reject()
	Promise { <rejected> undefined }
    
If run in node 6.6 and above(this doesn't include 7.0), we will something similiar:

	> Promise.reject()
	Promise { <rejected> undefined }
	> (node:91599) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): undefined
    
Now, if we run this in node 7.0, we get the deprecation warning:

	Promise { <rejected> undefined }
	> (node:91721) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): undefined
	(node:91721) DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.
    
    
So lets see another example of this:

	function resolvePromise() {
    	return rejectPromise();
	}

	function rejectPromise() {
    	return Promise.reject();
	}

	resolvePromise().then(() => {
    	console.log('resolved');
	});
    
    
Here we have 2 functions that return promises, one resolves, the other rejects.  We are calling the `resolvePromise` function and expecting it to resolve correctly, i mean, the name clearly states it resolves.

If we run this code, we will get the warnings from above,  and "resolved" will not be output to the console.


In previous version of node below 6.6, when no warnings were output,  it can be very confusing since no non-zero exit is returned.

Now this is not a one solution fixes all,  but it works in this case, you can just add a `.catch` to the end of statement and you should be good.  Our new code looks like this:


	function resolvePromise() {
    	return rejectPromise();
	}

	function rejectPromise() {
    	return Promise.reject();
	}

	resolvePromise().then(() => {
    	console.log('resolved');
	}).catch((err) => {
    	console.log('errored');
    });


Now when this is run, you should see the `errored` result in the console.

While most of us are perfect programmers,  can happen from time to time.  While running the test suite for [Szero](https://github.com/bucharest-gold/szero/issues/47), i got these warnings,  which made me write this post.

![](https://s-media-cache-ak0.pinimg.com/736x/26/fa/11/26fa112a511595bd9dca49db4ddbfbb0.jpg)
