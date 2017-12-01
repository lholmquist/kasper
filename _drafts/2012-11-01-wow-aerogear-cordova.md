---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Use Apache Cordova to convert to a hybrid app"
tagline: epicness, part 6
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW, Apache Cordova ]
published: true
group: wowaerogear
---


[Previously]({{ BASE_PATH }}{{ page.previous.url }}),  We traveled to Deadmines(HARD MODE) and added in some jQuery Mobile to make it feel like a more native experience.

## Hybrid Classes

We've been leveling this character up and everything is cool.  We are grouping with people and changing specs, and we are pretty powerful, but somethings are still missing.

We see these other classes that seem to do what we do, but also have more abilities.  We ask what class they are in group chat.  Most people just reply we snarky remarks,  but some nice person says, it's a Druid,  a Hyrbid Class.

We decide that we want to try this out.  We have some skills already and we can apply them to this new class.

You probably guessed it, we are going to take our current app, and convert it to a hybrid app using Apache Cordova.

## Cordova Setup

What is Apache Cordova you ask. Well, here is a blurb:

> "Apache Cordova is a set of device APIs that allow a mobile app developer to access native device function such as the camera or accelerometer from JavaScript. Combined with a UI framework such as jQuery Mobile or Dojo, this allows a smartphone app to be developed with just HTML, CSS, and JavaScript."

The first thing to do is to setup Apache Cordova on your system.  There are some really awesome tutorials on [AeroGear.org](http://aerogear.org/) that show the Apache Cordova setup.  I'd recommend going there if you don't have your system setup yet.

With our system setup, it's time to start our new character

First thing we need to do on the character select screen is to create a name for our new character.  You can name it anything you like,  but i'll name mine wowaerogear-cordova  .  With that name we start our adventure

Since i like to use the command line:

    $ mkdir wowaerogear-cordova


## Creating the Druid with Spec 1( Android )

First we are going to create the Android version.  Assuming that you have completed the quest to setup your system, we can continue.

Let's create a directory called "shared" directory inside the previous directory we just created:

    $ cd wowaerogear-cordova

Now it's time to create our new Druid Spec, and by that i mean our new Cordova Android Project:

	$ path/to/cordova/lib/android/bin/create ./android wowaerogear.android.cordova WowAeroGearAndroidCordova

Once the new project is created, you can test to make sure it worked by doing this:

	./bin/BOOM

*Just make sure you have a device plugged in or and Emulator running first*


So that takes us through the intro levels.  Time to do some questing and get our previous mobile web app into this new hybrid form.


[This Guide](http://aerogear.org/docs/guides/HTML5ToHybridWithCordova/) on AeroGear.org is excellent for converting our existing app to and Apache Cordova one.  Both Android and iOS.

Go ahead and do that quest.....I'll wait


...... OK now that we have completed that quest, our new environment should be all setup


All we need to do is copy our "web" files( css, js, html ) to the www directory in shared,  then add the reference to the cordova.js file in our index.html

	<script type="text/javascript" src="cordova-android-2.1.0.js"></script>

Now we should be able to run it:

	./bin/BOOM

What we see should be exactly the same as the previous web app, but this time it is installed on our Android device!!

*Note: We are still using the AeroGear.js library from the mobile app.*


## Creating the Druid with Spec 2( iOS )

Now that we have an Apache Cordova Android version, our next quest is to create the iOS version.  This should be super easy now that we have the other one setup.

So the first thing you do is go to your class trainer and by dual spec( that means you create a new project ).

To create the project:

    $ cd wowaerogear-cordova

    $ path/to/cordova/lib/ios/bin/create ./ios wowaerogear.ios.cordova WowAeroGeariOSCordova


This will give us our xcode project, but there is just thing we need to do.  If you remember from our previous quest, we need to set up a symbolic link to our shared www folder.

	$ cd ios
	$ rm -rf www
	$ ln -s ../../shared/www www


Ok,  now lets open up our project in xcode

With the project open, we need to add on file,  the ios cordova.js file.  Lets rename it to cordova-ios-2.1.0.js

Now we need to modify our index.html a little bit so it will load the correct JavaScript file depending on what device we are on.

	<script>
        if ( navigator.userAgent == "WowAeroGear Cordova Webview iOS" ) {
            document.write( '<script src="js/libs/cordova-ios-2.1.0.js"><\/script>' );
        } else {
            document.write( '<script src="js/libs/cordova-android-2.1.0.js"><\/script>' );
        }
    </script>

Now in the AppDelegate.m file, add this little block of code in the init function:

	NSDictionary *dictionnary = [[NSDictionary alloc] initWithObjectsAndKeys:@"AeroGearWow Cordova Webview iOS", @"UserAgent", nil];
    	[[NSUserDefaults standardUserDefaults] registerDefaults:dictionnary];
    	[dictionnary release];

The above code will modify the userAgent.

One more thing,  we need to "whitelist" the server so we can contact it.

We need to edit the Cordova.plist file.  This is located in the resources folder.

Under externalHosts, click the + symbol and put a "\*" for the value *this will whitelist all servers, in production you should be specific*


Now we should be able to run it.   Quest Complete!!

Now that our mobile app is installed on the device,  we can get access to more device things,  like the camera, accelerometer and some other things that the mobile web can't do.

## Next Steps

Now that you know how to play your new class,  try adding some class specific stuff, and by that i mean cordova lets us get access to native device features in our javascript.


## Coming Up

With Mobile Web and Hybrid Down,  It's time to start some endgame content. And by that i mean Native AeroGear Libraries!!!!!!!!    and one more just because !

[Fork the Source](https://github.com/lholmquist/WoWAerogear-cordova)

[Accept Summon to Next Post - iOS](/Personal/Aerogear/2012/11/14/wow-areogear-ios/)

[Accept Summon to Next Post - Android](/Personal/Aerogear/2012/11/29/wow-aerogear-android/)

*i know this isn't a cat but the internetz is running out of WoW related lolcats*

![](http://i.chzbgr.com/completestore/2009/2/19/128795107405299186.jpg)



