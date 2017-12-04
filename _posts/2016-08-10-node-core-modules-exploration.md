---
layout: post
title:  "Node Core Modules Exploration"
date:   2016-08-10
permalink: node-core-modules-exploration
categories: Node.js 
---

Node.js has a huge ecosystem of third-party modules and they are fairly easy to install using NPM .  

But Node also comes with several **Core Modules** that are available without installing any dependecies.  Some are pretty well known like the **http** module, and perhaps some are not so known,  like **punycode** or **zlib**

## The Idea

About 95% of my Javascript coding career has been focused on the browser.  Of course i've used node,  what with all the build tools for client-side development, but i never really paid close attention to how those really worked.

While perusing the Node.js API docs, https://nodejs.org/dist/latest-v6.x/docs/api/, I realized there were core modules here, that i didn't really know what they did.  

### The Goal

So i've decided to start this little bog series of exploring the current core modules.  I think my goal is to get a better understanding of what they do.  For example, coming from the client side, i've had no real experience in programming "network" stuff(udp, tcp type client/servers),  so when i see a module called, **dgram**, I'm like, "WTF is a dgram".   Although, you could argue, if i knew enough that udp and dgram are related, then i did KnowWTF a dgram was,  but lets not get to crazy :)


I'll just start at the top of the Docs list, so this first post will explore the wonderful and exciting world of .....

### Assert

According to the docs, this module "provides a simple set of assertion tests that can be used to test invariants."  Which is really just a fancy way of saying is it true or false.

It also states that "this module is intended for internal use by Node.js" and that it "is not inteded to be used as a general purpose assertion library".

That last bit i find interesting. I suppose that is why there are a bunch of external assertion libs.  Google "node assertion library" and you'll see.

It's interesting that this is actucally one of the Core node modules.  I've heard from someone, who talked to someone else, that the Node Core dudes and dudettes don't really want this in node core.  But since this API is at a "Locked" status, it ain't changin'


I'm not going to go into all the methods here, but lets just take a quick look at how to do a basic assertion.

Like most modules, you add it with `const assert = require('assert')`

Then we can do a quick test

	const assert = require('assert');

	assert(true);  // OK
	assert(1);     // OK
	assert(false);
	  // throws "AssertionError: false == true"
	assert(0);
	  // throws "AssertionError: 0 == true"
	assert(false, 'it\'s false');
	  // throws "AssertionError: it's false"


_the above was taken from the docs_


There are some other methods like `assert.strictEqual` , which makes sure we are testing with the `===` operator.

Or if you want to test that your function throws an error, you could use `assert.throws`

For more info on the other methods, check out the docs.


### Wrap Up

The assert module isn't to big and most of the methods are pretty easy to use.  It's interesting that there are so many other assertion libraries out there.  I suppose if i was motivated enough, i could look at the difference between them,  but i'm not.

**edit - added 8/10/16**

Something i did come across that i forgot to put in, is there is a library on npm called "assert".  And it has pretty big numbers for installs and such.  Although it looks like it is also used for client side stuff too.

Next up will be [Buffers](http://blog.lholmquist.org/node-core-modules-exploration-buffers/), so stay tuned


![](https://s-media-cache-ak0.pinimg.com/originals/a8/02/f5/a802f5035823e6cb4a10f835bb0e81ed.jpg)
