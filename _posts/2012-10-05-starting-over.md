---
layout: post
title: "Starting Over"
date: 2012-10-05 15:00:00
description: "The starting over process"
tagline: "Take Two - part 1"
description: "The story behind the story creator and where it's going"
categories: [Aerogear, JBoss, Ideas, Personal]
tags: [javascript, aerogear.js]
---



[Previously on Blog]({{ BASE_PATH }}{{ page.previous.url }}),  I decided to use Backbone.js for my UI layer and i also realized i didn't have security on my endpoints.  Time to start over.

## Redefine My Goals

Lets start by redefining what the goals of this application should be.  Lets start with the important parts:

* Mobile Only App( Web/Hybrid )
* REST Endpoints
* Jboss Backend

I guess you could call those the hardware requirements?  Probably not, but i'm going to anyway.

What parts of the actual app, Story Creator, are the most important parts( we're talking functionality here ):

* Create your own story
* Play with others
* Secure endpoints

The application has a little more functionality than that, but at its core,  that really what you want to do.  Create a story, and play it with a friend in a secure way.

## Story Creator

When i first started out creating this app, i had all the ideas in my head and that got a little messy when translating that to code.  So this time i'm going to write down what i want to create, and try to do each part separate( if possible ).

### What Can You Do?

Now that i've decided to take out Facebook integration to start off with, this is what we should be able to do:

* Create a User

* Create a Story

* Play with yourself / by yourself

* Show a list of story's playing / finished playing

* Play with other people

### What To Do First

As some one told me, [@abstractj](https://twitter.com/abstractj), defining api's first will help.  So that's what i'll do.  After this i'll probably move to the front end with some mocks.

## Up Next - Defining the API's

I know,  exciting............stay tuned

[Next Post](/Aerogear/JBoss/Ideas/Personal/2012/11/22/start-over-2/)

![lolcat](https://i.chzbgr.com/completestore/12/10/1/RVrGhFs-8EuRiLku_KD9pw2.jpg)
