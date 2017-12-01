---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Use Native iOS libs"
tagline: epicness, part 7
categories: [Personal, Aerogear]
tags: [aerogear-ios, REST, WoW, iPhone ]
published: true
group: wowaerogear
---


##Last Time

[Previously](/Personal/Aerogear/2012/11/01/wow-aerogear-cordova) , we tried out a Hybrid class(Apache Cordova) and saw how we can take our existing skills as a Web Developer and put them on multiple devices, using the same code base.

After some help from a certain brogrammer, yea, i'm talking about you [@mwessendorf](https://twitter.com/mwessendorf), we can now set off on our journey to create our app using the [Native AeroGear iOS](https://github.com/aerogear/aerogear-ios) libraries.

##Re-Rolling Native

Time to re-roll another class.  I told you i was an alt-aholic.  This time we are going for pure DPS, by that i mean we are going to be using Native iOS code, instead of a Hybrid class, which is Apache Cordova.

Time to create a warlock.  You might be saying, well what about a rogue,  they are pure DPS.  You would be correct, and we could.  Rogues are awesome, but, in the words of my cousin, "Locks are Nasty".

_since we are creating an iOS version, we are going to need a Mac running 10.7+ and xcode installed.
I also recommend this [great tutorial](http://aerogear.org/docs/guides/GetStartedwithAeroGearandXcode/) .
_

_you can also checkout the Android version [here](/Personal/Aerogear/2012/11/29/wow-aerogear-android/)_

##Character Select Screen

Time to create our new character.  First we need to open xcode and create a new project.  I selected a "Single View Application".  This might turn out to be a bad idea,  but i'm writing this while coding, so we'll see.

Then we just need to follow the bouncing ball and enter in some more info.  I left "use storyboards" unchecked and used my initials, "LPH", as the class prefix. _bet you can't guess what that P stands for_

Once the character is created, you should in the starting zone.  At this point you can click the run button, and you should see the simulator pop up with a somewhat blank screen.

##Starting Zone

Before we can set out to higher levels, there are some starting quests that we need to finish.  First we need to add the AeroGear iOS libs.  There are 2 ways to do it,  but since we want to breeze through this starting area, lets go for the easy route.  Do to this we will need an add-on, sort of like DBM or recount,  actually it's nothing like those.

We need the cocoa pod add-on,  this is for dependency management.  Check out [here](http://cocoapods.org/) for install instructions

Once we have this add-on, we need to add our dependencies.  We need to create a Podfile in our project directory.

    $ touch Podfile

Open this up and enter in the AeroGear dependency

    platform :ios

    pod 'AeroGear', '1.0.0.M1.20121115'

Save that and then at the command prompt, we need to install it:

    $ pod install

Once this is installed,  close the project we just created and then reopen it but this time using the new "Workspace" file that was created.

##Deeprun Tram

Now that we are out of the starting zone,  we need to take the Tram to Stormwind.  Yes,  Tram == AeroGear Pipeline.

If we open up our spell book, we should see a class called "LPHViewController" , depending on the class prefix you used, it will be different.  This is were we will add the pipeline and make our REST calls.

We are going to do a few things here:

1. Create a "base" URL

        NSURL* projectsURL = [NSURL URLWithString:@"http://us.battle.net/api/wow/"];

2. Create the pipeline object

        AGPipeline *pipeline = [AGPipeline pipeline:projectsURL];

3. Create a new pipe and set some config

        id<AGPipe> realmPipe;

        realmPipe = [pipeline pipe:^(id<AGPipeConfig> config) {
            [config name:@"status"];
            [config endpoint: @"realm/status"];
            [config type:@"REST"];
        }];

Now that we have our pipeline and pipe set up, we are going to call it,  but before we do that, we are going to need something to hold the data we get back,  how about an array,  OK.

    @implementation LPHViewController
    {
        NSArray* _realms;
    }

Now lets call our pipe's read method:

    [realmPipe read:^(id responseObject) {
        _realms = [responseObject objectForKey:@"realms"]; //need to do this so we can get the array

    } failure:^(NSError *error) {
        NSLog(@"An error has occured during fetch! \n%@", error);
    }];

This will call our endpoint and return us some data.  Very similar to the JavaScript library.  The objective c code might look different but the concept of connecting to the pipes are the same.


##Next Time

At this point, we aren't doing thing with the data.  Next time we will display the data return to the screen.  _this post is already long,  i like to keep them short_

The code is still in progress, but here is the [repo](https://github.com/lholmquist/WoWAerogeariOS) _be warned,  i'm a novice iOS brogrammer_

[Accept Summon to Next Post]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://4.bp.blogspot.com/_2qbnJJTuUtQ/TKruMlPTznI/AAAAAAAAAjM/FV_AHax_WRs/s1600/lolcatWow.jpg)

