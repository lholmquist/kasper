---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Use Native Android libs"
tagline: epicness, part 7
categories: [Personal, Aerogear]
tags: [aerogear-android, REST, WoW, Android ]
published: true
group: wowaerogear
---


##Last Time

[Previously](/Personal/Aerogear/2012/11/01/wow-aerogear-cordova) , we tried out a Hybrid class(Apache Cordova) and saw how we can take our existing skills as a Web Developer and put them on multiple devices, using the same code base.

You might be saying, "that wasn't last time, you started doing the iOS libs".  That is true,  but we are also going to be doing the Android libs in parallel.  Think of it as creating an Alliance( iOS ) and Horde( Android ) character at the same time.  Or for you Game of Thrones fans out there, how Feast For Crows and Dance With Dragons had parallel story lines.

##Re-Rolling Native

Time to re-roll another class.  I told you i was an alt-aholic.  This time we are going for pure DPS, by that i mean we are going to be using Native Android code, instead of a Hybrid class, which is Apache Cordova.

Time to create a rogue on the Horde side.  For this we will need the [AeroGear Android libs](https://github.com/aerogear/aerogear-android) .  There are prereqs to use this library, so be sure to read the README.  I'll wait


Ok, now that you've read that, clone the repo

     $ git clone git://github.com/aerogear/aerogear-android.git

Then "install" it

    $ mvn clean install

Since we are going to be creating an Android application, make sure you have the latest SDK installed and the android tools on your path.

To create the new Character(project) go to a command line and run something similar to the following:

    $ android create project --target 6 --name WoWAeroGearAndroid
                             --path ./WoWAeroGearAndroid --activity MainActivity
                             --package org.luke.wow.android

*we are going to be using Android 4.1, thats what target 6 is.*


##Early Levels.....Again

With our new character created, it's time to explore what this class(API) can do.  You can use any IDE that you want,  but i'll be using IntelliJ.  And since this post is about the AeroGear Android libraries, i guess the IDE doesn't matter,  just make sure whatever one you use, the aerogear-android lib is a dependency.

Ok, time to start questing.  First, and probably the most important part, we need to add this line to our AndroidManifest.xml file

    <uses-permission android:name="android.permission.INTERNET" />

Yup, you guessed right,  that allows us to get out to the internet

With that quest out of the way,  time to do the setup quests.  Since these are more android layout related, i won't go into them.   Just look at the code.

OK,  with those quests out of the way, time to start checking out this new API.

We are going to be adding things to the RealmFragment class, yes this was from the previous quests that i didn't show.

First,  we need some variables for our pipeline, pipe and the list view

    private Pipeline pipeline;
    private Pipe<RealmReturn> realmPipe;
    private List<Realm> realmList = new ArrayList<Realm>();
    private ArrayAdapter<Realm> adapter;

Lets talk a little about what each one is

###Pipeline

This object is similar to the JS version, it's just a collection of pipes

###Pipe&lt;RealmReturn&gt;

This is a single pipe, a connection, to the server.  We see that it will be a RealmReturn object, which has a List of Realms in it.

###realmList and adapter

These are just for the list view

##Connecting

Time to connect to our server,  lets take a look at the onCreate Method of this fragment class


    URL baseURL = new URL("http://us.battle.net/api/wow/");
    pipeline = new Pipeline(baseURL);

    PipeConfig pipeConfig = new PipeConfig(baseURL, RealmReturn.class);
    pipeConfig.setName("realms");
    pipeConfig.setEndpoint("realm/status");
    pipeline.pipe(RealmReturn.class, pipeConfig);

    realmPipe = pipeline.get("realms");


Again, lets take a look at each section.

###Base URL

This is just setting our , wait for it,...... the Base Url

And then we create our new Pipeline with this base URL

###PipeConfig

The pipe config is a neat object.  It allows us to customize our connection(PIPE) to the server.  As you can see we give it the object we want it to return into,  the specific endpoint we want to connect to, and the name, which in this case is just for referencing the pipe.(since the endpoint is set)


###realmPipe

Here we are just assigning this new pipe to our Pipe object


Now we are going to override the onStart Method and make the call to get our data

    @Override
    public void onStart() {
        super.onStart();
        getRealms();
    }



    private void getRealms() {

        realmPipe.read(new Callback<List<RealmReturn>>() {
            @Override
            public void onSuccess(List<RealmReturn> data) {
                realmList.clear();
                realmList.addAll(data.get(0).getRealms());
                adapter.notifyDataSetChanged();
            }

            @Override
            public void onFailure(Exception e) {
                //To change body of implemented methods use File | Settings | File Templates.
                e.printStackTrace();
            }
        });
    }


You will notice like the other AeroGear libs,  we have a read method, which has a success and error method for it's callback

Lets just deal with the success Method.  We can see that it returns our RealmReturn object it a List

If we were to look at this in a debugger, we would see that we have 1 value in this collection, which is another List of Realms

So now we can just get that Realm List and put it in the realmList variable that we bound to our list.  Then notify the Adapter that there are new values so our UI will update

##Next Time

Possibly finishing up to show the detail views.  Maybe

Also checkout the [iOS version](/Personal/Aerogear/2012/11/14/wow-areogear-ios/)

[Fork the Source](https://github.com/lholmquist/WoWAerogearAndroid)

![lolcat](http://icanhascheezburger.files.wordpress.com/2009/06/128906035426319379.jpg)


