---
layout: post
title:  "Node Core Modules Exploration - C/C++ Addons"
date:   2016-08-18
permalink: node-core-modules-exploration-cc-addons
categories: Node.js 
---

[Previously](http://blog.lholmquist.org/node-core-modules-exploration-buffers/) on blog, I went a little bit into the Buffer Module, next up..... Oh Crap,  it's C/C++ Addons


### C/C++ Addons

So this post is not going to go into how you create an addon.  There are tutorials on the interwebs, so just google them.

Also, according to the official docs: https://nodejs.org/dist/latest-v6.x/docs/api/addons.html

"the method for implementing Addons is rather complicated, involving knowledge of several components and APIs ". 

Then what are these things exactly?

Well, lets say you have some really cool C/C++ library that you wrote, and i mean,  who doesn't.  But now you are using node, but you still want to use that library you wrote.  Well addons are what you are looking for.

Basically, they are "used primarily to provide an interface between JavaScript running in Node.js and C/C++ libraries."

What this means, is that you can `require` them like any other module, and then interact with API that you have exposed in Javascript.

To learn more, check out the official docs: https://nodejs.org/dist/latest-v6.x/docs/api/addons.html

For a look at a production worthy Addon, checkout the [qt](https://github.com/arturadib/node-qt) module


This is a little like `process.binding`,  but with your own C++ code.

What is this `process.binding` thing you say.  Well just wait :)

In the next post, we will get back to "real" modules, like [Child Processes](http://blog.lholmquist.org/node-core-modules-exploration-child-processes/)


![](https://programmingsoul.files.wordpress.com/2012/05/lolcats-funny-pictures-questionmark.jpg)


