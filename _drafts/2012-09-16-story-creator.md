---
layout: post
title: "Story Creator - Part 1"
tagline: "The Idea"
description: "The story behind the story creator and where it's going"
categories: [Aerogear, JBoss, Ideas, Personal]
tags: [javascript, bootstrap, java, OpenShift]
---



## What is Story Creator?

That's a good question.  Let's start from the beginning and see where the idea came from.  One day while at work(MLMIC not Red Hat), 2 of my co-workers where in my cube trying to think of a cool idea for an app.  I had a couch and a big white board, so it was a good place to chill.  The app they were trying to come up with was basically a turn-based game.  After listening to them for a while i realized they were making the app to complicated.  Thats when a thought came into my head.  "I should go get some fritos from the vending machine".  After that thought i realized that a good concept for a turn based game would be where you would replace words from a story without knowing what the story was about.  And at the end you could read it and see how funny the story had become.

So yea, essentially Story Creator is my version of [Mad Libs](http://en.wikipedia.org/wiki/Mad_libs).


### The Essentials

I needed to define what the essential and most important parts of this app should be and what technologies i should use.  In no particular order:

#### Technology

* Java EE backend that would run on [openshift](https://openshift.redhat.com/app/)
	* REST endpoints
	* JPA?

* Javascript Frontend
	* Mobile Friendly(or just mobile?)
	* Backbone, Ember, something else for MVC?( I really wanted to learn something new.)

* Bootstrap

* MySql


#### Functionality

* Create Story

* Play Game
	* Play by yourself
	* Play with friends
		* Turn Based
		* Facebook Friends?

* Review Completed Games


* Connect to Facebook for sharing

	* Possible Facebook App?



With the thoughts written down i set out to create this app. [Part 2]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://i.chzbgr.com/completestore/2011/1/17/5d697fc6-680d-4169-954f-ed5fb9db0ecb.jpg)



_Note: The first few post won't have much code since ** Spoiler Alert ** i wasn't to happy with how the front end came out.  The backend i liked.  So the early posts will be about the idea and some of the things i used.  The later post should have more code once i start the refactor process. ** End Spoilers ** .  The code is [here](https://github.com/lholmquist/storycreator.git) for those looking for something.  Be Warned, it probably is broken. _
