---
layout: post
title: "Aerogear.js Meet Aerogear Controller"
description: "CORS with aerogear.js and aerogear controller"
tagline: Old Friends with New Tricks
categories: [Personal, Aerogear]
tags: [aerogear.js, AeroGear.Controller, jboss AS 7, CORS, M7 release]
published: true
group: wowaerogear
---


## Intro

This week marks another milestone release for the [AeroGear project with 1.0.0.M7](http://aerogear.org/news/2012/12/20/aerogear-1.0.0.M7-is-out/index.html).

Let's take a quick look at how we can now interact with AeroGear Controller by using AeroGear.js

_This example will be showing off the CORS functionality for both libs, so you will need to download 2 repo's.
Also, you will need JBoss As 7 for the server, so hurry up and [go get it](http://www.jboss.org/as7)._

### AeroGear Controller

We are going to be modifying the AeroGear Controller Demo, [repo here](https://github.com/aerogear/aerogear-controller-demo), for this example.


### AeroGear.js

The javascript library can be [downloaded here](https://github.com/aerogear/aerogear-js/archive/1.0.0-M2.zip) , or if you are using bower or yeoman, you can now use "bower install aerogear" or "yeoman install aerogear" respectively.

## Example

OK, with the setup out of the way,  lets start coding.

### Client Side

This part is actually quite simple using AeroGear.js, yes i know, shameless plug.  Creating the connection to the server will be no different than the other blog posts i've written and since everyone has read all of them in great detail, i should be able to skim a couple things.

For those of you that are new,  AeroGear.js is a javascript(duh) library that eases the development of client/server communication, with it's Pipeline, Data Manager and Authentication functionality.

I'm using sublime text 2 for this demo, but any editor will work.

First thing is to create a Pipeline and put a pipe in it.
_A Pipeline is a Collection of Pipes.  And a pipe is a connection to the server_


    var pipeline = AeroGear.Pipeline(
        {
            name: "controllerPipe",
            settings: {
                baseURL: "http://localhost:8080/aerogear-controller-demo",
                endpoint: "/cars"
            }
        }
    );

We will create this "baseURL" and "endpoint" in the server section.

Now, lets create an instance of our pipe and then make the call to the server

    var controllerPipe = pipeline.pipes.controllerPipe;

    controllerPipe.read({
        success: function( data ) {
           // do stuff
        },
        error: function( error ) {
            console.log( error );
        }
    });


If you run this,  make sure the controller-demo is running, you should get an error about a cross domain request failure.

If you look at the log message in the JBoss AS console, you should something similar to this:

    CORS Bad Request Headers: Received 'origin, content-type, accept', allowed: '[origin]'

The server is expecting origin in the header and thats it, but we are sending more that that, so lets fix this

## Server Side

Ok, lets switch gears( aerogear, snicker snicker) for a minute and checkout whats going on with the server side.

Open, the aerogear-controller-demo in an IDE of your choice,  I'm using IntelliJ here.

Since we are going to be looking at the Cross Domain functionality, the first thing we will do is modify the CorsConfinguration Object a little bit.  This is in the CorsConfigProducer.java file

Here is what it is by default:

    @Produces
    public CorsConfiguration demoConfig() {
        return CorsConfig.enableCorsSupport()
                .anyOrigin()
                .enableCookies()
                .maxAge(20)
                .enableAllRequestMethods()
                .build();
    }

What we need to do is add the "accept" and "content-type" to our allowed headers.

    @Produces
    public CorsConfiguration demoConfig() {
        return CorsConfig.enableCorsSupport()
                .anyOrigin()
                .enableCookies()
                .maxAge(20)
                .enableAllRequestMethods()
                .validRequestHeaders("accept, content-type");
    }

Ok,  that should fix that part,  but we still need to add one more thing.

Open up the Routes.java file and look for:

    .from("/cars").on(RequestMethod.POST)

This example is only going to deal with GET requests,  there is a JIRA for POST so stay tuned, so we will need to add a route:

    route()
        .from("/cars")
        .on(RequestMethod.GET)
        .produces(MediaType.JSON.toString())
        .to(Home.class).anotherPage();

Here we are saying, on a GET request from /cars, do the anotherPage method from the Home class and then return JSON

If you look at the "anotherPage" method, you will see this

    public Car anotherPage() {
        return new Car("silver", "delorean");
    }

All this does is return a new Car object with these params.  And since we told our route to produce JSON, it will be converted to JSON for us.


Now, after we redeploy our changes, we should be able to run our client and get this in return

    {color: "silver", brand: "delorean"}

This example is quite simple,  but it shows how easy it is to setup AeroGear Controller for CORS support.

Stay tuned for some more neat examples using these 2 technologies together


Modified Controller Demo(cors-js branch) : [Fork](https://github.com/lholmquist/aerogear-controller-demo/tree/cors-js)

Client Side Demo : [Fork](https://github.com/lholmquist/aerogear-controller-js-demo)

![lolcat](http://lolkitten.org/wp-content/uploads/2012/11/funny-cats-lolcats-christmas-is-coming.jpg)

