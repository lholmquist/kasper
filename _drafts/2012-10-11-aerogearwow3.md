---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Trying to use pipeline to connect to WoW REST api"
tagline: epicness, part 3
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW]
published: true
group: wowaerogear
---



## Westfall a.k.a.  AeroGear Data Manager

[Previously]({{ BASE_PATH }}{{ page.previous.url }}), we finished about half our quest.  We found out how to get data from a remote server using Aerogear.js, which required us to go on a group quest to the Aerogear Dev Mailing list.

Now that we have our pipe to our server, we need some place to store this data that we are going to receive.  This is where the AeroGear.js Data Manager will come.  Creating one is very easy actually:

	var stores = AeroGear.DataManager();
	stores.add( "realmStatusStore" );
	var realmStatusStore = stores.stores.realmStatusStore;

That's it for creating the Data Manager Store.  We are using the default adapter, which is an in memory array.  To add this to our pipe, all we have to do is add another parameter to the pipe's read method:

	realmStatusPipe.read( {
        success:function( data ) {
            console.log( realmStatusStore.getData()[0] ); // read the data here
        },
        stores: realmStatusStore //adding our new store
    });

Now that we have our data, lets put in on the screen just to do something else with it other than printing to the console.

	function updateRealmStatus() {
        var realms = realmStatusStore.getData()[ 0 ];
        var outsideList = $( "#realms" );
        _.each( realms.realms, function( realm ) {
            $( "<li>" ).append( "Realm Name:" + realm.name + " Realm Status:" + realm.status ).appendTo( outsideList );
        });
    }

this will produce a list of the realm names and their statuses.  Not very pretty, but it's a start.


## Next Up:

We travel to the Deadmines,  which means we make the UI nicer.

[Accept Summon to Next Post]({{ BASE_PATH }}{{ page.next.url }})


[fork the source](https://github.com/lholmquist/WoWAerogear)


![lolcat](http://i.chzbgr.com/completestore/2008/10/29/128698059620385666.jpg)
