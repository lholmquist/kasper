---
layout: post
title:  "ANNNDDDTHEEENNN..."
date:   2014-06-19
permalink: annndddtheeennn
categories: Javascript andThen Dude funny 
---

### Intro

I've been working with ES6 promises latelty,  and i'm really liking them.  

But everytime i use `.then`,  in my head i'm really saying, "aannnnndtheeeen"

_If this confuses you,  [check here](https://www.youtube.com/watch?v=GKNX6dieVcc)_


So i decided to make a little library that lets me do what i'm actually saying.

### AndThen

From the person who brought you "WoW Meet AeroGear" and "Aliens and iPhones,  HUH?" 

Say Hello to **AndThen**

It is basically a wrapper on top of ES6 promises.  But now,  instead of using `.then` and `.catch`

we can use `.annddtheenn` and `.noandthen`

Below are some examples

### Example:

Lets modify the "Chaining" and "Error Handling" example from the HTML5 rocks Promises article located [here](http://www.html5rocks.com/en/tutorials/es6/promises/)


    var promise = new AndThen(function(resolve, reject) {
      resolve(1);
    });
    
    promise.annddtheenn(function(val) {
      console.log(val); // 1
      return val + 2;
    }).annddtheenn(function(val) {
      console.log(val); // 3
    });
    

Error example:

    var jsonPromise = new AndThen(function(resolve, reject) {
      // JSON.parse throws an error if you feed it some
      // invalid JSON, so this implicitly rejects:
      resolve(JSON.parse("This ain't JSON"));
    });
    
    jsonPromise.annddtheenn(function(data) {
      // This never happens:
      console.log("It worked!", data);
    }).noandthen(function(err) {
      // Instead, this happens:
      console.log("It failed!", err);
    });
    


It's still a WIP and i want to implement the `Promise.all` method, which will be called `AndThen.andthenandthenandthen`

Fork the code here - [fork me](https://github.com/lholmquist/aaannnddtheenn)


![](http://uturncrossfit.com/wp-content/uploads/2014/04/and-then.png)


