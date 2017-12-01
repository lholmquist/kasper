---
layout: post
title: "Story Creator - Part 2"
tagline: "The Implementation"
description: "The story behind the story creator and where it's going"
categories: [Aerogear, JBoss, Ideas, Personal]
tags: [javascript, bootstrap, java, Backbone, JBoss Forge, OpenShift]
---



[Previously on Blog]({{ BASE_PATH }}{{ page.previous.url }}), I layed out the idea behind the Story Creator app.  A turn based version of Mad Libs that could possibly have a social aspect to it.  Possibly just mobile?

We these thoughts, I sat down, booted up my trusty Mac Book Pro, opened my IDE and then.... OH Crap!!  Where do i start this thing.


## Decision Time

I knew i wanted to use bootstrap for my Style's and Responsive Design framework and i knew that my backend was going to be RESTFul services running on JBoss AS 7,  but i still wasn't sure what javascript framework to use for the front end.

Looking at the available frameworks out there is very overwhelming.  [todoMVC](http://addyosmani.github.com/todomvc/) has an excellent list of these frameworks.  About the time i was researching what framework to use, JBoss released [JDF](http://www.jboss.org/jdf/), and [The JBoss Way](http://www.jboss.org/developer.html) initiative.  The Example app, Ticket Monster, was using Backbone, so i decided to try and go this route.

With that out of the way, i decided to map out the database schema, to get a better idea of how the game would work and what my RESTFul endpoints would look like.

### OpenShift

I decided to use OpenShift as my hosting, since it was free and gave me the option of using JBoss AS 7 and Mysql.  After the account creation, i created a new project, added the things i needed, and had an openshift project ready to go.

Since openshift uses git to deploy, i just created another git repo in my account and added a remote to save it there.

Now i needed to add REST, CDI, JPA and other things to get this thing working.


### Enter Forge

Openshift had given me a maven project.  So I need to add some things to my pom.xml for the other techologies i was going to use, but I can never remember what dependencies i need and the version goes with what.

This is where [JBoss Forge](http://forge.jboss.org/) comes in.  Forge is a rapid application development tool.  It's a little more than that, but thats not the point of this post.  Just checkout the website.

Forge runs on the command line, so for any of you young developers out there, learn the command line,  it just might save your life one day.  Actually, probably not, i have nothing to back that up with, but still learn it.

Here is a quick, abbreviated selection of some of the commands i used in forge:

    rest setup

    as7 setup

    beans setup

    ejb setup

    persistence setup

There is more to it than that,  but not much.

Now i had a nice maven project with all the things i needed for the backend to get started.

### Up Next:

The Front End - Take One.....[Part 3]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://i.chzbgr.com/completestore/2008/9/1/bringmeashr128647906558177409.jpg)




