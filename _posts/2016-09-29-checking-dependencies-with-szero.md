---
layout: post
title:  "Checking Dependencies with SZero"
date:   2016-09-29
permalink: checking-dependencies-with-szero
categories: Node.js 
---

One of the things that i feel makes Node(and NPM) so great is the ease in which you can add and use third party modules.  

As most node.js developers know, to start using an external module, you first install it

	$ npm install cool-module --save
    
Then we require it

    const coolModule = require('cool-module');
    
Then we use it 
	
    coolModule.doCoolStuff();
    
Yup, pretty easy.

However; as most node.js developers know, our dependency list in our pacakge.json can grow pretty quickly.  And some times we lose track of were in our code we are using these dependecies.

Sometimes, and dare i say it, we have modules in our package.json that we don't actually use. GASP!!!

Ok, so if you've made it this far, then you probably realize this next paragraph is going to talk about how Szero fits into what i wrote above.  

### What is SZero

SZero is a small library that we wrote(and when i say we, i'm talking about https://github.com/bucharest-gold), that checks the dependecies that you are using, and locates them in your code. 

Lets see an example.

To get started, we need to install, preferebly globally, but there is also an API that can be used

	$ npm install szero -g

Lets say i have a project with 2 dependecies, so my package.json would have something like this


    {
    ...
    "dependencies": {
        "coolModule": "^1.1.0",
        "other": "^0.1.0"
      }
      ...
     }
     
And in your code you've used these in various spots.

We can use SZero to generate a report to the console to show where these modules are used in our code

First we run the command and specify the directory

	$ szero /path/to/project
    
    ----------------------------------------------------------------------
	[ Declaration and file lines ]
	----------------------------------------------------------------------
	coolModule-require('coolModule'):
	./index.js:4
	./index.js:6
    
    otherModule-require('otherModule'):
    ./index.js:12
	----------------------------------------------------------------------
	[ Declaration and amount ]
	----------------------------------------------------------------------
    coolModule-require('coolModule') --> [ 2 ]    	
    otherModule-require('otherModule') --> [ 1 ]
	----------------------------------------------------------------------
	[ Unused dependencies ]
	----------------------------------------------------------------------
	None.
    

So what does all that mean.  Well, in the first section, we see where we required the module and what the variable name we stored it in.  We also see the line numbers where we use that variable

So since we stored the coolModule module in a varialbe called coolModule, that is why we get this format:

	coolModule-require('coolModule'):
    
In the Next section we count up the usage:

	coolModule-require('coolModule') --> [ 2 ]
    
And the last section shows, if any, unused dependecies.  

I think this feature is very useful. I know there have been many times where i install something, then don't end up using it, but forget to uninstall it

Speaking of unused dependencies, you can specify the `--ci` flag if you are running in a CI environment, so if any unused dependencies are detected, it will fail your build.

By Default, the output from running `szero` will output directly to the console.  But you can specify the `--file` flag to output to a file

So now lets see the command in action using an animated Gif

![szero-example](https://raw.githubusercontent.com/bucharest-gold/szero/master/out.gif)


### Code

If interested, the code can be found here on github:  https://github.com/bucharest-gold/szero

Create Issues, Submit PR's!!


Also, for those interested in seeing what types of Node.js things are being researched for JBoss and Red Hat, checkout our issues list here:  https://github.com/bucharest-gold/entente/issues


![lolcat](https://samanthacrawfordcasey.files.wordpress.com/2014/12/frozen-cat-86294205890.jpeg)
