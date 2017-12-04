---
layout: post
title: "AeroGear and WoW, Have a CORS"
description: "Use pipeline to connect to WoW REST api"
tagline: epicness, Redux Redux
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW, node.js]
published: true
group: wowaerogear
---


## Last Time

So [last time]({{ BASE_PATH }}{{ page.previous.url }}), we modified our code to use the official cross domain code in Aerogear.js .  Thats cool and everything, but since the WoW api doesn't suppport CORS, we could only use jsonp.  So lets create our own server that we can test against.


## Server Setup

Cross Domain calls really depend on the server that we are calling to.  If they don't know how to respond correctly, then all we are stuck.

For this example server i chose to use node.js with the express module.  All this is hosted on [Openshift](https://openshift.redhat.com/app/).  _Openshift uses node 0.6.0, which i'm not to happy about, but it works_

Ok, lets talk CORS for a minute. Basically the client will make an initial call to the server(Pre-flight) using the OPTIONS method to ask the server what they support.  The server needs to then respond with what methods are ok and what origin to allow.  This then gets sent back and then the client will send the follow up request if everything is ok.

_note that the browser does this pre-flight stuff for you_

Here is part of the node server,  specifically the OPTIONS part

    app.all( "/*", function(request,response) {

        // When dealing with CORS (Cross-Origin Resource Sharing)
        // requests, the client should pass-through its origin (the
        // requesting domain). We should either echo that or use *
        // if the origin was not passed.
        var origin = (request.headers.origin || "*");

        // Check to see if this is a security check by the browser to
        // test the availability of the API for the client. If the
        // method is OPTIONS, the browser is check to see to see what
        // HTTP methods (and properties) have been granted to the
        // client.
        if (request.method.toUpperCase() === "OPTIONS"){


            // Echo back the Origin (calling domain) so that the
            // client is granted access to make subsequent requests
            // to the API.
            response.status( 204 );
            response.header(
                {
                    "access-control-allow-origin": origin,
                    "access-control-allow-methods": "GET, PUT, DELETE, OPTIONS",
                    "access-control-allow-headers": "content-type, accept",
                    "access-control-max-age": 20, // Seconds.
                    "content-length": 0
                }
            );
            // End the response - we're not sending back any content.
            return( response.send() );
        }
        .......
        //Do Other request and response stuff
    }

Once the OPTIONS call is handled,  the original request will happen,  so here is some simple code to send back a response

    var responseBody = "{thing: 'crossDomain'}";

        response.status( 200 );
        response.header(
                {
                    "access-control-allow-origin": origin, //this is important
                    "content-type": "application/json"
                }
        );
        response.send( responseBody );

So all this does is send back this json object that we created.  Not to exciting, but it's an example.

Well what about doing a jsonp request,  how can we accommodate that?  Pretty easily actually.

    var callback = request.query.callback || request.query.jsonp;

First, we get the callback query param, it can be anything actually, since we can set that in our aerogear.js read method,  but we are only going to check for "callback" or "jsonp"


    if( callback ) {
        response.send( callback + "(" + responseBody + " ) " );
    } else {
        response.send( responseBody );
    }


Now, if there is a callback parameter, we need to pad the response, thats the "p" in jsonp.

OK,  so now if you make any request with aerogear.js to this [server](http://crossdomain-lholmqui.rhcloud.com/) , you should get:

    {thing: 'crossDomain'}

I'm using chrome, so in the dev tools, under Network,  you can see the OPTIONS and then a GET request, for example

## Proxy Time

To do this, we are going to basically use our server as a proxy that will then call the WoW rest api,  yea i know this is a bit overkill,  but contrived examples are awesome and we get to play with node.

I'm not going to go into how to call the wow api in node here,  so check out the code for that.  i'll link that at the end

## Call The Wow Rest API

So basically what we can do now with our existing Realm status app, is instead of pointing it at "us.battle.net" , we can now point it at "http://crossdomain-lholmqui.rhcloud.com/api/wow/" .  I haven't tried all the combo's, such as query parameters,  but that should work also.

So now we can make a call using jsonp or CORS.  So go forth and create cool shit!!


[Server Source](https://github.com/lholmquist/crossdomain)

[Realm Status Source](https://github.com/lholmquist/WoWAerogear)


![lolcat](http://rpuddle.files.wordpress.com/2010/11/lolcat_what.jpg)

