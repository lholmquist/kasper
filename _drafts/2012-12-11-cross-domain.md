---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Use pipeline to connect to WoW REST api"
tagline: epicness, Redux
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW]
published: true
group: wowaerogear
---


## Last Time

This is a supplemental post to this [one](/Personal/Aerogear/2012/10/10/aerogearwow2/);

Now that Aerogear.js officially supports cross domain calls, lets see how it works and how it differs from what we hacked together for the previous example.

## Lets Do It


Ok, so we create our pipeline and pipe in pretty much the same way.  Except, now there are now jsonp settings in the pipe

    var pipeline = AeroGear.Pipeline();

    pipeline.add([
        {
            name: "realmStatus",
            settings: {
                baseURL: baseURL,
                endpoint: "realm/status"
            }
        }
    ]);


So, now you are probably saying,  well how do i make the cross domain call.

Well, if your server is setup correctly for CORS,Cross Origin Resource Sharing, then things will work out of the box. So then you can do a POST and other verbs.  Lets save that for another post

_IE 9 will need a [plugin](https://github.com/jaubourg/ajaxHooks), but who really uses IE 9_


To do jsonp, you now specify that one the read method:


    pipe.read({
            success: function(){},
            error: function(){},
            jsonp: true
    });

That will default to the jQuery defaults

But, what if you want to not use a randomly generated call back name, and instead use a custom one,  and also change the query param that is appending to the url:

    pipe.read({
            success: function(){},
            error: function(){},
            jsonp: {
                callback: "jsonp",
                customCallback: "myCustomCallBack"
            }
    });



So yea,  pretty simple.  Again, if your server isn't setup for this kind of thing, then it won't really work

## Up Next:

Creating the server

[Accept Summon to Next Post]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://iliketowastemytime.com/sites/default/files/funny_cats_lol_cats_sup_bro.jpg)
