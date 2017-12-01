---
layout: post
title:  "Node Core Modules Exploration - Child Processes"
date:   2016-08-22
permalink: node-core-modules-exploration-child-processes
categories: Node.js 
---

[Previously](http://blog.lholmquist.org/node-core-modules-exploration-cc-addons/) on blog, i gave a little, and i mean little, overview of what C/C++ Addons are. Now onto Child Processes

### Child Process

This is a pretty cool module.  The basic gist of the module, is that it allows you to run commands from inside your node.js programs.

Like other modules, we need to require it, but we don't actually create instances of `ChildProcess` directly.  We use the `spawn`, `fork`, `exec`, or `execFile` methods to create new instances.

These methods are Asynchronous and there are synchronous version also.

While there are a few different functions, they all pretty much build upon 1 main function,  `child_process.spawn`

Lets see a quick example of `spawn`, first we get a new instance:

	const spawn = require('child_process').spawn;
    

Then we give it a command to execute:

	const ls = spawn('ls', ['-ltra']);
    
`spawn` actually takes 3 arguments.

The first is the command we want to run.

The second is an Array of the arguments we want to pass to the command. In this case we are passing `-ltra`

The third argument is an object of options, like setting the current working directory, `cwd`, and things of that nature.
    
The new process `ls` that is spawned, will have `stdout`, `stderr` and `stdin` ReadStreams Event Emitter thingies, so you can then listen for data to come in on them.

	ls.stdout.on('data', (d) => {
    	console.log(`stdout: ${d}`);
    });


Well, thats nice,  but what about these other functions.  It would be nice if we didn't have to listen for these `.on` events and just provide a callback.

This is where the `.exec` function would be used.

It is essentially the same as using `spawn`, but you can specfiy a callback

`exec` also has 3 arguments,  but they are slightly different.

The first is the command, but this time you actually add the arguments to the command.

The second is the `options` object

The third is the callback.

So taking that previous example.

	const exec = require('child_process').exec;
    
    exec('ls -ltra', (error, stdout, stderr) => {
	    console.log(stdout);
    });


The `execFile` is essentially a combination of `spawn` and `exec`.

There are 4 arguments this time.

The first is the command,

The second is the array of arguments.

The third is the `options` object

The fourth is the callback.

The difference here is it doesn't actually open a new shell when being executed, so it is slightly more effecient.  That is unless you are on windows, then you can't really use that.

Check out the docs for [why that is](https://nodejs.org/dist/latest-v6.x/docs/api/child_process.html#child_process_spawning_bat_and_cmd_files_on_windows)

### Getting Forking Crazy

Then there is this other crazy little function `fork`

It is much like `spawn`, but this will actually take a module, and execute it in a totally seperate Node instance with it's own instance of V8 and memory.  So needless to say, be careful with this one.

Another neat thing, is that a communication channel gets added to the returned ChildProcess, so you can pass messsages back and forth between the Parent and the forked processes.  

Lets take a look at simple example of using the `fork` function.

The parent:

	const cp = require('child_process').fork;
    
    const forked = fork('child.js');

	forked.send({message: 'YO'});

	forked.on('message', (m) => {
        console.log(`Child Message: ${m.message}`);
     });
     
     
And the child.js file:

	process.on('message', (m) => {
	    console.log(`Parent Message: ${m.message}`);

    	process.send({message: 'Back At Ya'});
	});
    
    
There are some more sophisticated things that you can do with fork, so check out the docs.


Next time we will take a look [at the `cluster` module.](http://blog.lholmquist.org/node-core-modules-exploration-cluster/)



![](https://blog.wyrihaximus.net/images/posts/T9I6A89.jpg)
