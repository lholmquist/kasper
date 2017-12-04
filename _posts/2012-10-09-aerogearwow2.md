---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Trying to use pipeline to connect to WoW REST api"
tagline: epicness, part 2
date: 2012-10-10 16:00:00
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW]
published: true
group: wowaerogear
---


## Early Quests

Now that we have [finished checking out the documentation]({{ BASE_PATH }}{{ page.previous.url }}), for both AeroGear.js and the WoW REST API, we can pick up some more quests and level up, getting us closer to a run through Deadmines.  And by Deadmines, i mean call this API from Aerogear.js so we can check our realm status.

## Our First Elite And Group Quest

We start coding and creating our pipelines and pipes with ease,  but then we come across Hogger, a level 10 elite, and by Hogger i mean "Cross Domain Requests".  I guess we didn't read the quest text close enough( probably because you were using quest helper ) and see that we need to use jsonp to overcome this foe.  But at the moment our pipes can't do this.

Well, the first thing we do is create an [example app](https://github.com/lholmquist/WoWAerogear) to test some things out with.  Since we really only want something for the front end ATM, lets use [yeoman](yeoman.io).

We look at our quest log and see that we need a couple things for this quest( not another gathering quest, damn it ).  First we add in aerogear.js.  The next thing we do is create a jquery ajax request using jsonp as a baseline to make sure what we want to do actually works, since jQuery is just like the "Sword of a Thousand Truths"

Here is what our code looks like so far:

	 var url = "http://us.battle.net/api/wow/achievement/2144";


	//a jquery version for a baseline to make sure it worked
	$.ajax( {
        type:'GET',
        url:url,
        contentType: 'application/json',
        dataType: 'jsonp',
        jsonp: 'jsonp',
        jsonpCallback: 'customCallback',
        success: function( data ){
            console.log( data );
        }
    });

we look at our console and see the result we except.  This quest is about half over.  We got the jquery one to work, but what about the aerogear one. Well, since it doesn't yet support jsonp, we need to add it.  That is the beauty of Open Source.  If we need something we can add it.

Looking at the jQuery example, we see that it looks a little ugly, we don't want to have to specify all those options.  That is the nice thing about aerogear.js(shameless plug),  it has sensible defaults, and if we need to override things, we can.

Let's start by seeing what we need to modify for jsonp to work and after that we can go into "Looking for Group"(Aerogear Dev Mailing List) and get some feedback to see if we are the right track before we send our pull request.

We decide that we would like to specify using jsonp at the creation of our pipe and want it to look something like this:

	var pipeline = AeroGear.Pipeline();

    pipeline.add([
        {
            name: "realmStatus",
            settings: {
                baseURL: baseURL,
                endpoint: "realm/status",
                jsonp: {
                    jsonp: 'jsonp'
                    //callback: 'customCallback'
                }
            }
        }
    ]);

Ah, much cleaner.  Now lets modify aerogear.js:

	AeroGear.Pipeline.adapters.Rest = function( pipeName, settings ) {
       .........
        // Private Instance vars
        var endpoint = settings.endpoint || pipeName,
            ajaxSettings = {
                // use the pipeName as the default rest endpoint
                url: settings.baseURL ? settings.baseURL + endpoint : endpoint
            },
            ........

            if( settings.jsonp ) {
                ajaxSettings.dataType = "jsonp",
                ajaxSettings.jsonp = settings.jsonp.jsonp ? settings.jsonp.jsonp : "callback";

                if( settings.jsonp.callback ) {
                    ajaxSettings.jsonpCallback = settings.callback;
                }
            }
        }

Now, when we use our aerogear pipe, we get the same as the jQuery one:

	var realmStatusPipe = pipeline.pipes.realmStatus;

    realmStatusPipe.read( {
        success:function( data ) {
            console.log( data );
        }
    });


Now that we have something that works, we send these idea's to the Aerogear-Dev Mailing List to get feedback so it will become part of Aerogear.js

## UPDATE - 12/11/12

Cross Domain Support is now official in Aerogear.js.[learn more](/Personal/Aerogear/2012/12/11/cross-domain/)  So basically everything you just read above is some what obsolete.


## Up Next:

Continuing to Westfall( Using the Aerogear Datamanager ).

[Accept Summon to Next Post]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://3.bp.blogspot.com/_fr44lIzC03k/TO3ZHO7d9HI/AAAAAAAACQ4/ZmolDiykmrE/s1600/lolcat+rainbow.jpeg)
