---
layout: post
title:  "Node Core Modules Exploration - Debugger"
date:   2016-11-28
permalink: node-core-modules-exploration-debugger
categories: Node.js Debugger 
---

In the previous post we checked out the **console** module.  Next on the list was Crypto, but that is a little more involved than i want to get today.

So this time we will take a look at the **Debugger** module, which isn't really a "module" module.  That is, you don't really `require` it anywhere.

Debugging is a really important part of programming.  And having some set of tools to help with that is way better than "console.log" debugging.

Lets take a look at what this module is all about.

To get all fancy and computer sciencey for those who like to use big words, this is an "Out of process" utility that is accesible using simple TCP based protocols.  That all that really means, is it's pretty cool and you can debug stuff.

#### Kicking it Old School

So how do we use this thing.  Simple. Lets say we have an `index.js` file like this

	Promise.resolve().then(x => {
    	console.log('resolve');
    })

We run it like this:

	$ node debug index.js
    
    
This will break on the first line and should show something like this:

![](/content/images/2016/Nov/Screen_Shot_2016_11_23_at_9_38_14_AM.png)

The debugger is now listening on port 5858 and it has stop execution on the first line,  awaiting your commands.

You are probably, "well wtf am i suppose to do now."   You can type `help` to see a list of commands

![](/content/images/2016/Nov/Screen_Shot_2016_11_23_at_9_40_15_AM.png)

As you can see,  there are a bunch of commands that you are able to enter in.  


If we wanted to set a breakpoint on that console.log of this code, we would just do

	setBreakpoint(2) // since its on line 2
    

If you are into cluttering your code with random stuff, then you could also use the `debugger` statement.  This essentially adds a breakpoint where ever that `debugger` statement is.


If you wanted to get fancy and you start node with the `--debug` or `--debug-brk` command, you can then attach a debugger later with some thing like this:

	$ node debug -p <process_id>
    
or 

	$ node debug <URI>
    
That URI is most likely `127.0.0.1:5858`

Remember before when i mention that TCP thing :)



There are some UI tools like [Node Inspector](https://github.com/node-inspector/node-inspector) that make using the debugger easier.

#### Another Way

For those of us who come from Front-end dev and who have always used the Chrome Devtools, this is just crazy talk.  

Well now there is an experimental feature that was added to node not to long ago.  This added integration with the V8 inspector, so now you can use chrome devtools to debug and profile.  

So using that first example, we start node like this:

	$ node --inspect --debug-brk index.js
    
We the `--debug-brk` flag to break on the first line and use the `--inspect` flag, this will give us a chrome-devtools url to go to:

`chrome-devtools://devtools/bundled/inspector.html?experiments=true&v8only=true&ws=127.0.0.1:9229/6f2a631a-83e0-4c31-9126-0c65a3a193eb`

And now we can do our normal debugging() in the the Dev tools


#### List

So while doing this post, i came across a small little bug in the `list` command.

What is the `list` command you say.  Lets take a look at that, then we will see the bug.

Lets say we have a file called letters.js with the contents:

	const a = 'a';
    const b = 'b';
    
    console.log(a, b);
    
and then we run this with the debug command to enter the debugger.

	$ node debug letters.js
    


We get something similiar to the picture from a little earlier in the post.

We can then type `list()` and press enter, this will print out the 5 lines before and after where the current cursor is of the current script.  _note: since we didn't give list any arguments, it defaults to 5_.


We should see something like this:

![](/content/images/2016/Nov/Screen_Shot_2016_11_28_at_3_44_16_PM.png)

`list` also takes a parameter, we could have passed a number like `list(10)` and it would list the 10 lines before and after.  Although in this case,  we would get the same result.

There is something though,  that sticks out.  Thats line 6,  `});`

If we look at our source code, we only have 4 lines, so what is that?

Well, we will get more into this in the post about Modules(coming soon), but basically when node loads a module, it actually wraps that module in a IIFE.  

If we take a quick look at the source code for the [debugger modules list method](https://github.com/nodejs/node/blob/c1aa949064892dbe693750686c06f4ad5673e577/lib/_debugger.js#L1107) we see that we are removing this.

If we were to comment out this if statement and run our code with our new special node, we could get something like this:

![](/content/images/2016/Nov/Screen_Shot_2016_11_28_at_4_01_16_PM.png)

So, that last line we were wondering about is actually the closing of that IIFE.  

I've created an [issue](https://github.com/nodejs/node/issues/9768) and sent a [PR](https://github.com/nodejs/node/pull/9773), so the process has been started to try and remove that little guy.  


#### Wrapping up

There are some other neat little utilities that can be run, like `scripts`, which will print out the currently loaded scripts in the debugger.

For more info about the Debugger, checkout the [official docs](https://nodejs.org/dist/latest-v6.x/docs/api/debugger.html)  


![](https://s-media-cache-ak0.pinimg.com/564x/0c/2c/8f/0c2c8f16c9f43bff0747d58753462480.jpg)


I know, i broke form and didn't use a cat picture here.  
