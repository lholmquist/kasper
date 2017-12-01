---
layout: post
title:  "Node Core Modules Exploration - Command Line Options"
date:   2016-10-24
permalink: node-core-modules-exploration-command-line-options
categories: Node.js 
---

Last time we checked out a little bit of what the [cluster module had to offer](http://blog.lholmquist.org/node-core-modules-exploration-cluster/). 

What we will be checking out this time, isn't necessarily a module, but it is listed with the other modules, so it's next on the list.

I'm of course talking about **Command Line Options**.  To [quote the official docs](https://nodejs.org/dist/latest-v6.x/docs/api/cli.html#cli_node_debug_module)

	Node.js comes with a variety of CLI options. These options expose built-in debugging, multiple ways to execute scripts, and other helpful runtime options.
    

The ones that i find i'm using the most are the `-v` and `-h` flags.  These are the familiar `version` and `help` flags respectively.

Using the `-i` or `--interactive` flag brings into the node REPL. (also can just call `node`).

The REPL has access to the "core" module, but what if we wanted to test out a third-party module inside the REPL.

This can be done with the `-r` or `--require` flag.  It takes another argument which is the module name or the path to the module.  This can be repeated if multiple modules are needed.

Lets see an example.  Lets take this cool Star Wars API node client that some cool dude wrote, [Swapi Node](https://www.npmjs.com/package/swapi-node) and have it be available in our REPL

So in my current directory i've already done an `npm install`, or `yarn`, i can't keep up with these things, so I have my node_modules.  `-r` uses the same rules as `require`, so it will look in our node_modules directory for the `swapi-node` module.

Then in my REPL, i can do a `const swapi = require('swapi-node')` and everything will work



#### V8 Options


One neat flag is the `--v8-options` flag.  While, not to exciting by itself, all it does is print out the list of flags that you can pass to V8(thats the c++ part of node).  But you can use these flags to enable experimental ES features, for example.

Running node with this: `node --harmony`, will run node with all the new almost-completed ECMAScript features.  _note: features might not be totally stable_

Anything that is "in progress" can be turned on with their own specific flag.  To see what those "in progress" features are:

`node --v8-options | grep "in progress"`

I'm running node 6.5.0 locally, so that command produces:

	--harmony_array_prototype_values (enable "harmony Array.prototype.values" (in progress))
	  --harmony_object_observe (enable "harmony Object.observe" (in progress))
	  --harmony_function_sent (enable "harmony function.sent" (in progress))
	  --harmony_sharedarraybuffer (enable "harmony sharedarraybuffer" (in progress))
	  --harmony_simd (enable "harmony simd" (in progress))
	  --harmony_do_expressions (enable "harmony do-expressions" (in progress))
	  --harmony_regexp_property (enable "harmony unicode regexp property classes" (in progress))
	  --harmony_string_padding (enable "harmony String-padding methods" (in progress))


To actually see what V8 version is running with your version of node, you can use the `-p` flag, which evalutes an argument as Javascript and then prints the result.

`node -p process.versions.v8`

There are also V8 flags to do some real computer sciencey C++ stuff too.


#### Environment Variables

Along with the cli flags, there are some envrinonment variables that can be set also.

For example, we could use the `NODE_DEBUG=module` to print out debug info when using the loading modules.

Another really cool one is the `NODE_REPL_HISTORY`,  this can be set to a file so we can have a persistent REPL history.  Now the default for this is `~/.node_repl_history`, but if it is set to an empty string, "" or " ", then your history is not saved.

Next up we check out the [console module](http://blog.lholmquist.org/node-core-modules-exploration-console/)

![](http://thatlinuxbox.com/blog/images/articles/cat-meme-your-console_1.png)
