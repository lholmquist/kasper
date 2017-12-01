---
layout: post
title:  "Node Core Modules Exploration - Buffers"
date:   2016-08-17
permalink: node-core-modules-exploration-buffers
categories: Node.js Buffers 
---

[Previously](http://blog.lholmquist.org/node-core-modules-exploration/) on blog, i gave a little intro of what this series will be about, and we checked out the Assert module. Now onto Buffers

### Buffers

I'll be honest.  When i see that something returns a buffer or a method takes a buffer, i get a little bit scared.  

I mean, working with Binary data is pretty scary.  Coming from doing client side JS, it is really low level stuff.

But if you want to succesfully interact with file system operations and octet stream type things, like TCP, then they are sort of a necessity. 

Something interesting about Buffer, is that it is a Global inside node, which means you don't need to actually do a `require('buffer').Buffer`  and the api is marked as Stable.

I'm not going to go to deep into each method, but somethings have changed with regard to creating new buffers that should be said.

Before node 6, you would create a Buffer using the `new` keyword, and what the Buffer allocated was based on what was passed in.

So for example, to create a new Buffer from a string:

	const b = new Buffer('Hello World');
    
    console.log(b.toString()); //Hello World
    

Now that node 6 is live, this way has been deprecated in favor of the new `Buffer.from`, `Buffer.alloc`, and `Buffer.allocUnsafe`.  It is recommended that developers migrate to this new way

So the above example now becomes:

	const b = Buffer.from('Hello World');
    
    console.log(b.toString()); //Hello World
    

Just a quick note here, the default encoding is `UTF-8`

It is important to note that once a Buffer is created, it cannot be resized.

You can use the `alloc` method to create a new buffer of a particular size, and then fill it in later.

	const b = Buffer.alloc(10);
    
    b.fill('Hello World');
    
    console.log(b.toString()); //Hello Worl
    
note that the 'd' got left off becuase i used 10 for the size.

If you wanted to get crazy, you could also use the `allocUnsafe` method.

Don't worry, you won't need to wear a condom or anything while using it,  but you are not guarenteed to get a new zero filled buffer.

So you would need to extra step of filling it in.  Supposedly it is faster to create Buffers this way, but i don't really have any fancy numbers to back that up.  I would recommend checking out the docs here:  https://nodejs.org/dist/latest-v6.x/docs/api/buffer.html#buffer_class_method_buffer_allocunsafe_size


There are plenty more methods that deal with manipulation and interacting with Buffers, and you can check those out on the official docs.  

https://nodejs.org/dist/latest-v6.x/docs/api/buffer.html#buffer_buffer


Next up, [C/C++ Addons](http://blog.lholmquist.org/node-core-modules-exploration-cc-addons/)



![](http://images5.fanpop.com/image/photos/24700000/cute-buffer-don-t-forget-to-pay-him-lol-cats-24781738-500-375.jpg)
