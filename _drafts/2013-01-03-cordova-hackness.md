---
layout: post
title: "Concept: AeroGear Apache Cordova Plugin"
description: "A Hacky attempted at creating an Apache Cordova Plugin"
tagline:
categories: [Personal, Aerogear]
tags: [aerogear.js, AerGear iOS, Cordova]
published: true
group: wowaerogear
---



## Intro

Right before Christmas break i wrote down some thoughts on what an AeroGear Apache Cordova plugin could be like.  My inital thought was about the DataManager in the AeroGear libs.  The idea was that we could use iOS CoreData( using the AeroGear iOS libs adapter that is currently being written) and have AeroGear.js have a "Core Data" adapter or something, that would make the calls for us.

You can read more [here](http://aerogear-dev.1069024.n5.nabble.com/aerogear-dev-Cordova-Plugin-Idea-td1079.html)


## Pre Hack Time

Not being that familiar with how these plugins are created, i figured i should start figuring.  The story that follows is true.  _disclaimer:  Don't try this at home kids.  I am a bit of an expert in these things( little Peppa Pig reference there.)   Also,  it's really hacky_


I decided to make a plugin based on the Pipeline concept that AeroGear has been working on.  I would create a new pipeline  adapter type, lets call it cordova, in the js lib.  this would wrap the client side cordova stuff, so i can use the familiar methods that AeroGear provides us in the REST adapater.

This would then connect to the iOS side and use the AeroGear iOS Pipeline REST lib to actually make our calls to our server.

Not really sure there is any benefit to doing this.  Could possibly be a bad idea,  but this is a learning exercise, so here we go.

Before we start really hacking, we are going to need a base iOS Apache Cordova Project.  I'm using version 2.1.0, so hurry up and go create one.  Doc's [here](http://docs.phonegap.com/en/2.1.0/guide_getting-started_ios_index.md.html#Getting%20Started%20with%20iOS)

You will also need to add the AeroGear iOS libs to the project.  Easiest way is using CocoaPods.  Here is a [good tutorial](http://aerogear.org/docs/guides/GetStartedwithAeroGearandXcode/) on that

## Hack Time - iOS

Ok,  now that we have a base project,  time to create our plugin.  _The New project should be opened with the workspace file once you add the AeroGear libs in._

Ok, We are now in XCode.  We need to create a new file in the Plugins directory.

+ Right-click or File>new will work.
+ Choose Objective-C class, then click "next"
+ Name the class.  _Make sure it is a Subclass of CDVPlugin_
+ Then follow the bouncing ball until the new Files are created.

You should end up with 2 files.  a .h and .m

    #import <Cordova/CDV.h>

    @interface AGPlugin : CDVPlugin

    @end

The .h file should look similar to the above,  you might have to change the import,  i did.

Now, lets add in a method in the .h file, this one is the Read method

     - (void) read:(CDVInvokedUrlCommand *)command;


Now we just need to implement this file in the .m file

    #import "AGPlugin.h"
    #import <Cordova/CDV.h>
    #import <AeroGear/AeroGear.h>

    @implementation AGPlugin

    - (void) read:(CDVInvokedUrlCommand *)command {

    }


    @end

We should have something like the above.

Not to exciting yet, so lets add in some code.  I'm going to hard code things in during the first pass just to get things working.  But what do we need.  Well, since we are familiar with how the AeroGear iOS Pipeline works,  we know we need a Pipeline that has some pipes in it and then we need to call the read method and define some callbacks.

So hears our config:

    id<AGPipe> pipe;
    AGPipeline *pipeline = [AGPipeline pipeline:[NSURL URLWithString:@"http://us.battle.net/api/wow/"]];
    pipe = [pipeline pipe:^(id<AGPipeConfig> config) {
        [config name:@"status"];
        [config endpoint:@"realm/status"];
    }];



We are going to be getting data from the WoW REST api again.

Now lets call the read method:

    [pipe read:^(id responseObject) {
        CDVPluginResult* pluginResult = [CDVPluginResult resultWithStatus:CDVCommandStatus_OK messageAsDictionary:responseObject];
        NSString* javaScript = [pluginResult toSuccessCallbackString:command.callbackId];
        [self writeJavascript:javaScript];

    } failure:^(NSError *error) {
        NSLog(@"An error has occured during fetch! \n%@", error);
    }];


Hmmmm,  what's this CDVPlugin and writeJavascript stuff. This is the cordova lib.  At this point we are just going to pass stuff back to the client.

Now, we just need to add a couple things to some configurations and we can then move onto the client side stuff.

We need to add our need plugin to the Cordova.plist so the plugin can become discoverable to our application.

In the the plist file, add a new entry under plugins.
the Key/Value pair in my case is AGPlugin/AGPlugin . this depends on your Class name

Before we close this file,  we need to open access to the interwebs.

+ create a new entry under ExternalHosts and put a * in the value field


## Hack Time - Javascript

Now it's time to create our new AeroGear.js adapter that will call this plugin.  _I've added the most recent aerogear.js and jquery.js to the project_


I created a new file called cordova.js,  yes, this is sure to confuse me later.  I copied the contents from the rest adapter and pasted it into this new file, since a lot of it can be reused.


First thing i did was to change the Constructor and changed ajaxSettings to connection Settings,  probably didn't need to do the last part:

    AeroGear.Pipeline.adapters.Cordova = function( pipeName, settings ) {
        // Allow instantiation without using new
        if ( !( this instanceof AeroGear.Pipeline.adapters.Cordova ) ) {
            return new AeroGear.Pipeline.adapters.Cordova( pipeName, settings );
        }

        settings = settings || {};

        // Private Instance vars
        var endpoint = settings.endpoint || pipeName,
            connectionSettings = {
                // use the pipeName as the default rest endpoint
                url: settings.baseURL ? settings.baseURL + endpoint : endpoint
            },
            recordId = settings.recordId || "id",
            type = "Cordova";

            this.getConnectionSettings = function() {
                return connectionSettings;
            };

        /**
            Returns the value of the private recordId var
            @private
            @augments Rest
            @returns {String}
         */
        this.getRecordId = function() {
            return recordId;
        };
    };


Now, since i was keeping this simple for the moment,  i only changed the read method:

    AeroGear.Pipeline.adapters.Cordova.prototype.read = function( options ) {
        var that = this,
            recordId = this.getRecordId(),
            connectionSettings = this.getConnectionSettings(),
            url,
            success,
            error,
            extraOptions,
            hackyOptions;

            .......

        hackyOptions = $.extend( {}, this.getConnectionSettings(), extraOptions );

        cordova.exec(
            hackyOptions.success,
            hackyOptions.error,
            "AGPlugin",
            "read",
            hackyOptions.args || []
        );


The "cordova.exec" makes the call to the iOS native code.

it takes a success and error callback, the plugin class name, the method i want to call, and then any arguments i pass in.


Now we need to add this new js file to our index.html, after the aerogear.js loads:

    <script type="text/javascript" src="cordova-2.1.0.js"></script>
    <script type="text/javascript" src="js/jquery-1.8.3.js"></script>
    <script type="text/javascript" src="js/aerogear.js"></script>
    <script type="text/javascript" src="js/cordova.js"></script>
    <script type="text/javascript" src="js/index.js"></script>

Now we just need to call this thing.

So in our inde.js file,  this should be already created from the cordova setup,  add the pipeline/pipe creation in the recievedEvents function

    var thing = AeroGear.Pipeline({type:"Cordova"});
        var pipe = thing.add("name").pipes.name;
        pipe.read({success:function( data ) {
                  console.log("Yea");
                  console.log(data); }
                  ,error: function(err){console.log("bah")}});



Yea, i know,  ugly spacing



We should be able to now run this in the simulator.  If everything goes ok,  we should see in the output console of xcode, the json payload from the REST endpoint


## Next Time

Next time, lets make this thing a little more dynamic


Repo Here: [link to repo](https://github.com/lholmquist/AerogearCordovaConcept)


![lolcat](http://images6.fanpop.com/image/photos/32500000/lolats-lol-cats-32524610-400-299.jpg)


