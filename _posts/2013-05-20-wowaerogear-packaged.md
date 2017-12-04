---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Converting the web app to a chrome packaged app"
tagline: epicness, part ?( Chrome Packed App )
category: articles
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW]
published: true
group: wowaerogear
---


## Intro

"Just when i thought i was out,  they pull me back in" - Micheal Corleone( Godfather III ).

I thought i was done with the WoW series.  But there is one more thing.  The shiny object of the day today was creating a chrome packaged app.

I wanted to see if the AeroGear.js library worked here.  So time to begin.


## Starting

So there are a couple things that we need to add/modify.

Well what is a Chrome Packaged App,  this link will answer that: [Find Answers](http://developer.chrome.com/trunk/apps/about_apps.html)

To create a Packaged app, we need a Manifest file and a background file to launch our main app.

lets create the manifest.json first


    {
        "manifest_version": 2,
        "name": "AeroGear WoW",
        "version": "1",
        "permissions": [],
        "app": {
          "background": {
            "scripts": ["background.js"]
          }
        },
        "minimum_chrome_version": "26"
    }


Let's take a look at the important part of the contents for a moment.

The _name_ should be a no brainer

there is a _permissions_ array,  nothing in here at the moment

the _app_ variable specifies the background file that we are going to load to launch our app

and the _minimum chrome version_ variable tells us,  yup you guess it,  the minimum version of chrome this will work with


Now lets create that background.js file


    /**
     * Listens for the app launching then creates the window
     *
     * @see http://developer.chrome.com/apps/app.window.html
     */
    chrome.app.runtime.onLaunched.addListener(function() {
      chrome.app.window.create('index.html', {
        id: 'main'
      });
    });


Here we are listening to the launch event and once that happens we create a new window for our app to live in.

There are many options you have, but we are just going to specify an id



So now we should be able to run our app.

1. first we need to install it, type this in your location bar in chrome

    chrome://extensions/

2. then click on the Load unpacked extension...  button

3. Navigate to the directory where the app resides

4. The app should now show in the list of apps.

5. Then click Launch


If you right-click on the app to open the dev tools,  you will notice that there are some errors.

This has to do with chrome's CSP

Since this is a packaged app,  we don't need jsonp,  so we can remove that from this call

    function readPipe() {
        $.mobile.loading( "show" );
        realmStatusPipe.read( {
            success:function( data ) {
                realmStatusStore.save( data, true );
                updateRealmStatus();
                $.mobile.loading( "hide" );
            },
            error:function( data ) {
                console.log( data );
            }//,
            //jsonp: {
            //    callback: "jsonp"
            //} //set to true to use jsonp , DUH
        });
    }


The next thing is that in order to do cross domain requests,  we need to set the permissions.

in the manifest.json file,  add the URL to the permissions array


    "permissions": ["https://us.battle.net/api/wow/"]

_You will notice that we using https.  as per the CSP, we can't access cross domain requests that are not https_



Now if you reload the app,  you should see no errors.  but there is one thing that is still missing.  you may notice that you can't scroll the list


to fix this, all you have to do is add a simple style to the html element in index.html

    <html style="overflow-y:scroll">


now if you reload the app,  things should work as expected.  YEA!!


[fork the source](https://github.com/lholmquist/WoWAerogear/tree/chrome)

![lolcat](https://i.chzbgr.com/maxW500/7319510528/h1C99D050/)



