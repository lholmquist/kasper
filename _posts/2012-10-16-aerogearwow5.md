---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Trying to use pipeline to connect to WoW REST api"
tagline: epicness, part 5
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW]
published: true
group: wowaerogear
---



## Deadmines - Hard Mode

[Previously]({{ BASE_PATH }}{{ page.previous.url }}),  We traveled to Deadmines and made our UI nicer.  But now let do Deadmines Hard Mode and add in jQuery Mobile to make it look more like a native app.

The first thing we need to do is go and download the [jQuery Mobile library](http://jquerymobile.com/).  You could also just link to a CDN, but for this post we are just going to use the downloaded libraries.  Now that we have those, lets update our index.html file and remove the bootstrap code and add in the JQM code.

	<head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>jQuery Mobile: Demos and Documentation</title>
        <link rel="stylesheet"  href="styles/jquery.mobile-1.2.0.css" />
        <script src="//ajax.googleapis.com/ajax/libs/jquery/1.8.2/jquery.min.js"></script>
        <script>window.jQuery || document.write('<script src="scripts/vendor/jquery.min.js"><\/script>')</script>
        <script src="scripts/vendor/jquery.mobile-1.2.0.js"></script>
    </head>

Very nice.  With this added we need to now create some "Pages".  With JQM we can create these "Pages" in the same index.html file.  All we need to do is add data-role="page" to our div's.  JQM will automatically display the first one it finds and hides the others.


	<div data-role="page" id="main">
        <div data-role="header" data-position="fixed">
            <h1>Realm Status</h1>
            <a href="#" data-icon="refresh" data-iconpos="notext" id="refresh">refresh</a>
        </div>
        <div data-role="content">
            <ul data-role="listview" id="realms" data-filter="true" data-filter-placeholder="Search Realms"></ul>
        </div>
        <div data-role="footer" data-position="fixed">
        </div>
    </div>


Ok, so this our basic layout for the first page. Our other pages will also be similar. As you can see we got rid of the table and converted it to a ul item.  You can also see that JQM helps us separate our header, content and footers easily.

With this setup, lets jump into our app.js file and see what we need to do to make this first page come up.  We modify our read method a tiny bit so we can get JQM's loader to come up and then hide it after the data is loaded.

	function readPipe() {
        $.mobile.loading( "show" );
        realmStatusPipe.read( {
            success:function( data ) {
                updateRealmStatus();
                $.mobile.loading( "hide" );
            },
            error:function( data ) {
                console.log( data );
            },
            stores: realmStatusStore
        });
    }

Our updateRealmStatus mostly stays the same,  but we do have to update our buildTable function to create li items instead of table items:

	function buildTable( realm ) {
        var row = $( "<li>" );
        row.append( $( "<a>" ).append( realm.name ).attr( { id: realm.slug } ) );
        return row;
    }

Once this function returns we need to call the refresh method of the listview so JQM can convert it:

	outsideList.listview( "refresh" );

This should now give us a list of all the realms.


Now that we have this, we can add some navigation, so when we touch a realm we can see the detail on that.  If you want to see how it was done checkout the code.  There are still some things that aren't to pretty that will be updated soon.


Since the real purpose of this quest, er, i mean blog was to show AeroGear.js in action,  i'm not going to go into the details of JQM.


## Up Next

Now that we have finished Deadmines,  it's time to go to some higher level Zones.  What good be in store???????

[fork the source](https://github.com/lholmquist/WoWAerogear)

[Accept Summon to Next Post]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://2.bp.blogspot.com/_tlJ1qktf2lA/THBp5YFKX7I/AAAAAAAAACM/4T7S57yjcBo/s1600/funny-pictures-your-cat-is-almost-level-68.jpg)
