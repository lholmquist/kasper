---
layout: post
title: "Facebook App - Multipost"
tagline: "Defining the Models"
description: "A way to post to multiple Facebook groups"
categories: [Ideas, Personal]
tags: [javascript, facebook]
published: false
---


## Intro - The Problem

My wife, who is a yoga instructor, was posting a flyer and information about an upcoming Pre-natal yoga class she was teaching on her Facebook groups.  She belongs to a bunch of "Mom" and other "Mom related" groups.  The problem that she had was that she wanted to post the same thing to all these groups, but there was no native way of doing this.  It was a very manual process.

_If there is a way, then anyone reading this can ignore this post_


So i thought, that would be a good little project to play with the Facebook Graph API:

Make an app, that would allow you to choose which groups, from your list of groups, you want to post to.  All at the same time.

This could also be extended to friends.

## Beginnings

OK,  before i started, i needed to a place to host this app.  For this i'm using Openshift.  I created a node app since node is cool and all this code should be client side, so no need for a java app server.  And besides, node is really cool

Now, i needed to go over to [facebook developer portal](https://developers.facebook.com/) and create a new mobile web app.

I'll let you do that yourself.  Just follow the bouncing ball _note: there isn't actually a bouncing ball on the site_ .

The documentation is pretty good about getting started.

This first bit of code is actually taken from their docs:

     <div id="fb-root"></div>
    <script>
      window.fbAsyncInit = function() {
        FB.init({
          appId      : 'APP_ID', // App ID
          channelUrl : '//YOUR_DOMAIN/channel.html', // Channel File
          status     : true, // check login status
          cookie     : true, // enable cookies to allow the server to access the session
          xfbml      : true  // parse XFBML
        });

        FB.Event.subscribe('auth.statusChange', handleStatusChange);
      };

      // Load the SDK Asynchronously
      (function(d){
         var js, id = 'facebook-jssdk', ref = d.getElementsByTagName('script')[0];
         if (d.getElementById(id)) {return;}
         js = d.createElement('script'); js.id = id; js.async = true;
         js.src = "//connect.facebook.net/en_US/all.js";
         ref.parentNode.insertBefore(js, ref);
       }(document));
    </script>


So if we look at this code a bit, we see that we need to substitute the APP_ID with whatever the app_id is that facebook assigned to the new app.

The other piece, the channel file, is actually a easy thing but for some reason, is not in the tutorial.  So all you need to do is create a channel.html file and add this one line to it

    <script src="//connect.facebook.net/en_US/all.js"></script>

The YOUR_DOMAIN should be where ever the app is going to be hosted and match the domain you put in the app settings on the developer site

For testing, i just use localhost:8080


Next part is adding in the callback functions that are not yet defined

    function handleStatusChange(response) {
      document.body.className = response.authResponse ? 'connected' : 'not_connected';
      if (response.authResponse) {
        console.log(response);

        updateUserInfo(response);
      }
    }

    function updateUserInfo(response) {
     FB.api('/me', function(response) {
       document.getElementById('user-info').innerHTML = '<img src="https://graph.facebook.com/' + response.id + '/picture">' + response.name;
     });
   }

Basically, this will just show my profile picture and my name once i am logged in.  If you do a console.log() of the response, you can see what it gives you, or go to the Facebook Graph Explorer on the developer sight

The big part of this is the

    FB.api( '/me', ... )

This is the Facebook API to get my data.  If you put an ID in instead of /me, then you can get data for that person,  i believe Mark Zuckerburg is actually /4,  which is odd

## Getting Down to Business

So what i want to do is get access to my groups.  First, we need to make sure that we have the appropriate permissions.  Accessing groups is actually an extended permission, so we need to the user to ok that first.

    function loginUser() {
     FB.login(function(response) { }, {scope:'email, user_groups,publish_stream'});
     }

So here is a login function.  That second bunch of variables tells the FB api what permissions to ask the user for









