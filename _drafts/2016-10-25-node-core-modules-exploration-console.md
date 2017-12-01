---
layout: post
title:  "Node Core Modules Exploration - Console"
date:   2016-10-25
permalink: node-core-modules-exploration-console
categories: Node.js 
---

In the [previous post](http://blog.lholmquist.org/node-core-modules-exploration-command-line-options/) we took a look at some of the command line options available when starting node.

Now we are going to take a look at probably the most used core module, especially for those just getting started in debugging.  Thats right, `console`

This is another "Global" module, so require is not, ummm,  require'd here

I'm sure this is very common in everyones code:

	console.log('It Worked');
    

And if you've ever done client side(html, js) programming, then `console` and most of its methods should be familiar.

While `console` is on both browsers and Node.js, don't expect them to be completely implemented the same way.

For example, the `console.assert` in Node will throw an `AssertionError` on a falsey value instead of printing the message out, which is what the browser will do

#### Console Class

The `console` module has a `Console` class that can be used to create loggers whos output can be configurable.

`new Console(output, errorOutput)`

In fact, the global `console` is a special console.  It is acutally the same as calling this:

`new Console(process.stdout, process.stderr)`

We could create a simple logger that outputs to a file.  This example is taken from the [official docs](https://nodejs.org/dist/latest-v6.x/docs/api/console.html#console_new_console_stdout_stderr)


	const output = fs.createWriteStream('./stdout.log');
    const errorOutput = fs.createWriteStream('./stderr.log');
    
    const logger = new Console(output, errorOutput);
    
    logger.log('Say Something Cool');
    // in stdout.log:  Say Something Cool
    
    
#### Timings

`console` has 2 methods that can be used to compute the duration of an operation.  They are `console.time` and `console.timeEnd`.  Both take a `label` as an argument. Timers are identified by this label, so you would need to use the same labels for `time` and `timeEnd`.

	console.time('myTimer');
    
    someFunctionThatTakesTwentyThreeMS();
    
    console.timeEnd('myTimer');
    // prints myTimer: 23.232ms


Just a quick note on the `timeEnd` function in versions of node prior to 6.0.0. You could actually call `console.timeEnd` multiple times with the same label, this wasn't intended.  Newer versions will delete the timer once `console.timeEnd` is called.



#### Other Neat things

There are 2 other sort of neat things, those are `console.dir` and `console.trace`.


Lets take a look at `trace` first.  It is pretty simple, but can be powerful.  It prints to `stderr` or where ever you have your `err` outputting, the string `Trace: <Stack Trace of current position in code>`

Here is an example from my REPL:

	> console.trace('Trace Me');
	Trace: Trace Me
    at repl:1:9
    at sigintHandlersWrap (vm.js:22:35)
    at sigintHandlersWrap (vm.js:96:12)
    at ContextifyScript.Script.runInThisContext (vm.js:21:12)
    at REPLServer.defaultEval (repl.js:313:29)
    at bound (domain.js:280:14)
    at REPLServer.runBound [as eval] (domain.js:293:12)
    at REPLServer.<anonymous> (repl.js:504:10)
    at emitOne (events.js:101:20)
    at REPLServer.emit (events.js:188:7)


This next one is something i always forget is there.  `console.dir`

If you pass an object into `console.dir`, it prints out the String representation of that Object.(It uses the util.inspect function behind the scenes).

There is also some options you can pass.  For example, if an object is large and complicated, use the `depth` option, to make inpsect recurse through the object.

The depth defaults to 2, so with this example:

	const a = {
    	a: 1, 
        b: {
        	c: 2, 
        	d:{
            	e: 3, 
                f: { 
                	d: 4
                }
            }
        }
    };
    
    console.dir(a);
    
    //outputs: { a: 1, b: { c: 2, d: { e: 3, f: [Object] } } }
    
If i wanted to see what that `[Object]` is, then i could do:

	console.dir(a, {depth: 3});
    
    // outputs: { a: 1, b: { c: 2, d: { e: 3, f: { d: 4 } } } }


But what if i had more depth, then passing `null` will recurse idefnitely.



#### One more tidbit

console functions are actually asynchronous.


Next up is a very easy topic,  Crypto


![](https://i1.wp.com/farm3.static.flickr.com/2443/3993932273_80298b0f47.jpg)

