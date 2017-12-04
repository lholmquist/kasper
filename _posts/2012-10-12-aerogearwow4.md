---
layout: post
title: "Aerogear, meet World of Warcraft"
description: "Trying to use pipeline to connect to WoW REST api"
tagline: epicness, part 4
categories: [Personal, Aerogear]
tags: [aerogear.js, REST, WoW]
published: true
group: wowaerogear
---


## Enter Deadmines - Making the UI nicer

[Previously]({{ BASE_PATH }}{{ page.previous.url }}),  We travel to Westfall( Used Datamanger to manager our data).  We also displayed it to the screen.

Now we enter the Deadmines, and try to make our UI a little nicer and display some relevant info.  Well, what is relevant info.  If we take a quick look at the current [realm status thing](http://us.battle.net/wow/en/status) from Blizzard, we see that the name, status, type, population are the main things.  Lets start with that.


Lets update our function to update the realm status.  We are going to use a Table tag now instead of a list, so we need to change that.

	function updateRealmStatus() {
        realmStatusStore.save(realmStatusStore.read()[ 0 ].realms,true);
        //did the above because datamanger can't filter beyond one layer yet
        var outsideList = $( "#realms" );
        _.each( realmStatusStore.read(), function( realm ) {
            buildTable( realm ).appendTo( outsideList );
        });
    }

We also added in a separate function to build the rows and columns:


    function buildTable( realm ) {
        var row = $( "<tr>" );

        row.append( $( "<td>" ).append( $( "<span>" ).append( realm.status ? "Up" : "Down" ).addClass( "label" ).addClass( realm.status ? "label-success" : "label-important" ) ) );
        row.append( $( "<td>" ).append( realm.name ) );
        row.append( $( "<td>" ).append( realm.type ) );
        row.append( $( "<td>" ).append( realm.population ) );

        return row;
    }

You may notice that we are changing the label based on th realm status.

Now we update the body of the index.html to look like this:

	<div class="container">
        <div>
            <h1>Realm Status</h1>
            <table id="realms" class="table table-striped table-condensed">
                <thead>
                    <tr>
                        <th>Status</th>
                        <th>Name</th>
                        <th>Type</th>
                        <th>Population</th>
                    </tr>
                </thead>
            </table>
        </div>
    </div>


And now we have a cool Realm Status Mobile App.  You may be saying, "Why is this the Deadmines,  UI change was easy".  Well, think of it as Deadmines with all heirloom gear.

## Up Next

Deadmines: Hard Mode.  we add in some jQuery Mobile and have some navigation to see more about a realm.

[fork the source](https://github.com/lholmquist/WoWAerogear)

_note:  The next post totally changes the UI(think of it as a revamp of the talent tree),  i've tagged the bootstrap version as 1.0 in github_

[Accept Summon to Next Post]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://lolcat.com/pics/purplzkitty.jpg)


