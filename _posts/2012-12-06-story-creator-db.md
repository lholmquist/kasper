---
layout: post
title: "Starting Over - Part 3"
tagline: "Defining the Models"
description: ""
categories: [Aerogear, JBoss, Ideas, Personal]
tags: [javascript, mongodb]
published: true
---


[Previously on Blog](/Aerogear/JBoss/Ideas/Personal/2012/11/22/start-over-2/),  Defining some Server API.


##Models

Time to define some models. NO, not that kind of model. I wonder how many people will close this page, once they realize what this post is actually about.

I have a bit of a background in relational database work, but i really would like to use a "NoSQL" approach.  So I think i'll try out MongoDB, but thats for another post

This story creator application will be nothing without data on the backend.  Lets list some of the tables/entities/models/collection,  i guess it all depends on what your using, but basically these are all the same.

1. Story
2. Game
3. User
4. Players
5. Parts Of Speech


OK, time to define these one by one,  i'm going to call these tables from now on, and consider it a given that they will all have an "ID" field as there PK and possible a "Version" field, that would be more a JPA thing,  but who knows.

###Story

* title - String - The Title of the Story, duh
* body - String - The Body of the story, modified with the substitutions of speech parts
* originalBody - String - The Original Body of the Story
* shortDescription - String - A summary of what the story is about
* createDate - Date - The date the story was created
* maxPlayers - Number - The number of players that can play together
* user - User object - One to One - The User who created this story, can be null/anonymous

###Game

* complete - boolean - is the game completed or not
* lastPlayed - Date - The last time the game was played,  essentially the last update date
* createdBy - User - One to One - The user who created the game, can be null/anonymous
* story - Story - One to One - The Story that they will be playing
* players - Players - Many to Many - The list of players, playing this game.
* speechParts - SpeechParts - Many to Many - The list of parts of speech that are in play

###User

* userId - String - An Identifier for the user, probably should be username, used to be the facebook ID
* firstName - String - duh
* lastName - String - duh

_probably should be some sort of password field thing here.  Originally it was using facebook authentication_

###Players

* playerId - String - This should actually be the same as userId, when it was a FB app, you could play your friends, even if they have never played this app.
* name - String - the name of the player
* currentTurn - boolean - is it this players turn or not

###Parts Of Speech

* playedBy - User - the user who played this part of speech
* playedDate - Date - the date played
* valuePlayed - String - the value that was played
* fieldId - String - ?

I think the original thought for this table would be that it was populated on game creation and then updated as the game went on.


With my tables defined, i can possible start the coding process?  maybe


![lolcat](http://i.chzbgr.com/completestore/2010/9/29/3edb46df-73ac-4383-9b9c-409cd1184fad.jpg)











