---
layout: post
title: "Convert to Cordova"
description: ""
category: articles
categories: [Aerogear, Cordova, JUDCon]
tags: [Cordova, Cool Cool, AeroGear, JUDCon]
published: true
---



## Intro

Last Week, I gave a talk about "Converting Mobile Web Apps to Hybrid Using Apache Cordova" at JUDCon in Boston.  For those who want to follow along or who were at the talk,  this post will get more indepth on the demo i showed.

## Start Conversion.

The first thing to do is to have a web app that you want to convert.  Here we are going to convert a web app that i created for my daughter.  It is pretty simple,  it's a canvas app that you can draw on.  She's 2 1/2, so she was excited.  I used Yeoman to scaffold out the app.   This is a nice tool because it set up a grunt build for me.

If you are following along at home, then you can install yeoman with:

    $ npm install -g yo grunt-cli bower

_You can use whatever tool you want to create your html5 app._

[code at this point](https://github.com/lholmquist/judcon2013/tree/1.0.1) ,  sort of

She was excited to use this web app on her ipad but then she came to me and said, "Dad,  this is a cool app,  but you are hosting it locally and i want to play it when i am in the car on my ipad,   can you convert it to a native app?"

While i know some iOS programming,  i thought it might be easier to use Apache Cordova and use my existing skills as a Web App developer to create this native app.

### Create a New Cordova Project

The first thing we need to do for this conversion is to create a new Cordova project.  I decided to keep this also as a web app, so i'm going to create a cordova directory in my current app directory.

    $ mkdir cordova

    $ cd cordova

Then i'll create the app using the Cordova CLI.  If you don't already have the cordova CLI tool installed, then:

    $ npm install -g cordova

Then to create a new project

    $ cordova create .

Here we are just creating a default project in the current directory,  we could also add optional id and package params.

To see more options, run:

    $ cordova --help

Now we have a base cordova project in our web app

Converting is pretty easy actually.  In the cordova directory, there are a few sub directories that are created.  The one that is we are interested right now is the www/ directory

Basically we want to copy the html/css/javascript in our app/ directory and then paste that in the www/ cordova directory.

Your index.html file should look similar

[index.html](https://github.com/lholmquist/judcon2013/blob/1.1.0/cordova/www/index.html)


#### Add a Platform

Adding a platform, such as iOS is simple also. All we need to do is run this command:

    $ cordova platform add ios

and then to prepare it( basically a copy of the www/ directory to the ios specific platform folder )

    $ cordova prepare ios

[code at this point](https://github.com/lholmquist/judcon2013/tree/1.1.0)


### Setup Grunt to Automate Copy

Well, this is pretty cool since it isn't really that hard to copy and paste.  But copy and pasting and keeping things in sync can be a pain.  This is where grunt helps.

We can set up grunt to copy the files from our app/ directory and then paste them in our www/ directory.  We can exclude the index.html and index.js files since they will be different in cordova and don't want to overwrite them

In our Gruntfile.js, the copy: command should look similar:

    copy: {
            dist: {
            ...
            },
            cordova: {
                files: [{
                    expand: true,
                    cwd: '<%= yeoman.app %>',
                    dest: '<%= yeoman.cordova %>',
                    src: [ "**", "!index.html", "!**/*.txt", "!**/index.js" ]
                }]
            }
        },


It would also be nice if we could automate the prepare calls as well.

Grunt to the rescue again.  We can add the grunt-shell module and now run shell commands.  so lets add that to our build

    shell: {
            cordovaPrepare: {
                command: 'cordova prepare ios',
                options: {
                    stdout: true,
                    execOptions: {
                        cwd: '<%= yeoman.cordova %>'
                    }
                }
            }
        }

[code at this point](https://github.com/lholmquist/judcon2013/tree/1.1.1)


### Add New Functionality

Now that we have our app setup, lets starting adding the new functionality that we can get from a Hybrid App.

We are going to add this new functionality in the cordova/js/index.js file since this is Cordova App related and not Web app related.  This is just my preference, you can do it differently if you want.  In the words of abstractj,  "Follow You Heart"

First thing we can do is now get access to our connects.  This is somewhat simple:

    var options = new ContactFindOptions(),
            fields = ["displayName", "name", "emails"],
            success,
            error;

        options.multiple=true;

    navigator.contacts.find(fields, success, error, options);

We have a few variables here.

the first is the fields we would like to get back from the native contacts api.

Next is the success and error callbacks. Success returns the contacts in a JSON array

and the last is options.   Here we are saying we want multiple rows.  Not sure why that isn't the default.


[code at this point](https://github.com/lholmquist/judcon2013/tree/1.2.0)


This tag adds in the photo album access code

[code at this point](https://github.com/lholmquist/judcon2013/tree/1.3.0)


Here we add the ability to save what we draw on the canvas to the photo album

[code at this point](https://github.com/lholmquist/judcon2013/tree/1.4.0)


Here we add in the REST service

[code at this point](https://github.com/lholmquist/judcon2013/tree/1.5.0)


Here is what the final code looks like

[final code](https://github.com/lholmquist/judcon2013)






