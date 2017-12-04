---
layout: post
title:  "Safari Push Notifications"
date:   2014-11-05
permalink: safari-push-notifications
categories: 
---

As of Safari 7, which was released last year with OS X Mavericks, Apple's Push Notification Service(APNs) can now send Push Notifications to web pages.

And since i am a dev on the AeroGear team, the ones responsible for the awesomeness that is the Unified Push Server, i really wanted to see support for Safari.

Well it's been a year and it's not there yet :),  but will be very soon.  So lets see what needs to be done in order for this to work

## Apple Setup

Of course, for you to setup this all up,  you will need to be either an iOS or Mac developer(this means you need to give apple $99).

First thing is to make your way over to [Apple's developer portal](https://developer.apple.com) and create a new application WebSite Push ID

![add website push id](/content/images/2014/Nov/Screen_Shot_2014_11_05_at_10_31_17_AM.png)

Once done,  you will now need to add a certificate to it,  which can be done by clicking on the edit button

![](/content/images/2014/Nov/Screen_Shot_2014_11_05_at_10_33_55_AM.png)

Now you will need to download the certicate

![](/content/images/2014/Nov/Screen_Shot_2014_11_05_at_10_38_42_AM.png)

Open it, which should open in the Keychain Acesss app.

You will then need to find the certificate in the list,  and export it to a .p12 format

![](/content/images/2014/Nov/Screen_Shot_2014_11_05_at_10_43_18_AM.png)

Once you save it, you will be asked to add a passphrase.  Don't forget it, we will need it soon

That should be it for the developer center

## Unified Push Server Setup

Let head over to setting up the Unified Push Server.  Since this part is still in development, we will need to clone it and build it ourselves

Here is the [safari-push branch](https://github.com/aerogear/aerogear-unifiedpush-server/tree/safari-push)

Once all cloned and in the proper branch, you can run:

    mvn clean install
    
    
With any luck it will be built successfully.  

You can follow the [instructions here](https://github.com/aerogear/aerogear-unifiedpush-server/tree/safari-push#deployment-to-wildfly) on how to deploy it to wildfly or eap

Also checkout the [Unified Push Server User Guide](http://aerogear.org/docs/unifiedpush/ups_userguide/) on how to login and get started.

If you are running on localhost, you should be able to go to http://localhost:8080/ag-push

Once logged in,  create a new application,

Then you will need to create a new Variant.  Guess what one we are going to choose :)

![](/content/images/2014/Nov/Screen_Shot_2014_11_05_at_11_09_49_AM.png)

We need to upload the certificate and passphrase that we exported earlier.

For Safari, you will need to choose the "Production" choice for type.

Once added, it will be added to the variant list

## Web Setup

For the web setup,  there are 2 parts.
   
1. Setup a RESTFul web service
2. Add the some Javascript code to our website


### Web Service Setup

So probably the hardest part of this whole process, we need to set up a RESTful Web Service that Safari will talk to when our web page requests permission to register a user for push notifications.

Oh yea, and guess what, it needs to be https, and no, you can't use a self-signed cert either.

So the easiest way to do this is some sort of PaaS solution.

For this example, i'm using [Openshift](https://www.openshift.com/).

And since i am a javascript dev, i've created a small node.js server to serve these REST endpoints.

Here are what the endpoints look like, https://github.com/lholmquist/safari-push-example/blob/master/server.js#L45

### The Push Package

I won't go into to much detail here, but when the user asks for permission, Safari will try to download a "push package" to determine if everything is ok,  read more about how to create it here:

[Creating a Push Package](https://developer.apple.com/library/mac/documentation/NetworkingInternet/Conceptual/NotificationProgrammingGuideForWebsites/PushNotifications/PushNotifications.html#//apple_ref/doc/uid/TP40013225-CH3-SW7)

### Web Page

This next part is not to hard, since we are just adding some javascript to our page to 

* determine if the brower supports push notificaitons
* grant access

Here is how we ask for permission:

https://github.com/lholmquist/safari-push-example/blob/master/public/scripts/app.js#L23


Once permission has been granted,  we can take the device token that is return, and send it to the Unified Push Server and register to receive notifications.

## Sending a Notification

To finally send the notification, lets use the node.js sender client.

While it can be installed from npm using `npm install unifiedpush-node-sender`

Things have changed slightly and these changes are not yet on npm, so lets clone and build it ourselves.

https://github.com/aerogear/aerogear-unifiedpush-nodejs-client

after cloning, make sure to run `npm install` first.

now we can modify the example.js and settings.json files

In settings.json add the applicationId and masterSecret from the UnifiedPush Server when you created the Application:

    {
        "url": "http://localhost:8080/ag-push",
        "applicationId": "12345",
        "masterSecret": "12345"
    }
    
    
In example.js:

    message = {
        alert: "Hi",
        sound: "default",
        title: "i'm the title",
        action: "view",
        badge: 2,
        'url-args': ['arg1']
    };
    
    options = {
        ttl: 3600
    };

    agSender.Sender( settings )
        .send( message, options, function( err, response ) {
            if( err ) {
                console.log( err );
                return;
            }
            console.log( "success called", response );
    });



If things went ok,  then you should receive a push notification.


## Wrap Up

Since this has not yet been merged, some of the screenshots might be in flux.

but this allow peeps to play for now.



![](http://www.deltaattack.com/wp-content/uploads/2011/07/legend_of_zelda_cat.png)



