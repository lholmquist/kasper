---
layout: post
title:  "Getting In Sync"
date:   2015-03-18
permalink: getting-in-sync
categories: AeroGear Javascript aerogear.js Sync 
---

## The Sync API

Last week saw the first Alpha release of the [AeroGear Sync Server](https://github.com/aerogear/aerogear-sync-server/releases/tag/1.0.0-alpha.1).

Our Sync Server is an implementation of the [Differential Synchronization Algorithm](http://research.google.com/pubs/pub35605.html) by Neil Fraser

The Main new feature of this JS release is the Sync Server Client SDK.

The SDK has 2 flavors of engines to interact with the Sync Server:  

[JSON Patch](https://github.com/Starcounter-Jack/JSON-Patch) and [Diff Match Patch](https://code.google.com/p/google-diff-match-patch/)

In our [JS cookbook](https://github.com/aerogear/aerogear-js-cookbook/tree/master/diff-sync-ember), We have created an Example that uses the **JSON Patch Engine**

Lets see an example:

    syncClient = AeroGear.DiffSyncClient({
        serverUrl: 'ws://localhost:7777/sync',
        onopen: function () {},
        onsync: function () {}
    });

Here we call the `DiffSyncClient` constructor with some options. You will notice that we are using Websockets as our transfer protocol.

By default,  the **JSON Patch** engine will be used.

Then to add a "Document" which is really just an object, we can call the `addDocument` method

	syncClient.addDocument(someObject);
    
If we need to get that document back out of the engine, we can use the `fetch` method:

	syncClient.fetch(someDocumentId);
    
And when we make changes to our document, we call the `sync` method:

	syncClient.sync(someChangedObject);
    
Each of the above methods will result in the `onsync` callback to be called

We define a specific format for the document:

	var document = {
	    id: ...,
	    clientId: ...,
    	content: { ... }
	};


This New API is currently marked as experimental, so we encourage everyone to try it out and let us know what works and what doesn't.



## Polyfill Removal

Prior to this release, we were embedding the [ES6 Promise Polyfill](https://github.com/jakearchibald/es6-promise).

Now that almost all Major browers are [supporting Promises natively](http://caniuse.com/#feat=promises) we've decided to not include it.

If you still need to support a browser without native support,  you will need to include the polyfill


## Deprecations and Removals

#### Authz

This release we removed the **Authz** module completely from the library.  This module was marked as experimental and it didn't really add much value anyway.

#### AeroGear.ajax

The **AeroGear.ajax** module, which was just an internal thing, has also been removed.  There was only one place it was being used,  the **Unified Push Client SDK**,  so it didn't make sense anymore to have this as a standalone thing.  


#### Notifier

We also started the Deprecation process for the **Notifier** Module.

The abstraction that notifier provides no longer makes sense.  The code will eventually be removed in a Major release.


## Get the Code

The usual spots still apply.

Source Code:

https://github.com/aerogear/aerogear-js/releases/tag/2.1.0


Bower:

	bower install aerogear
    
There are some "pre-built" components that can be installed with `bower` from the [AeroGear Components repo](https://github.com/AeroGear-Components)


![](http://1.bp.blogspot.com/_M0cuxJsSjdU/TVKuqPLeXqI/AAAAAAAAPCQ/uzbEhpxijw4/s1600/funny+pictures+-+NEW+OLYMPIC+EVENT+FOR+2012+++++SYNCHRONIZED+CARPET+SWIMMING.jpeg)

## Release Notes - AeroGear JavaScript - Version 2.1.0
    
<h2>        Bug
</h2>
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-289'>AGJS-289</a>] -         Diff Sync Example doesn&#39;t build due to Ember CLI error
</li>
</ul>
                    
<h2>        Enhancement
</h2>
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-265'>AGJS-265</a>] -         Remove base64 polyfill
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-266'>AGJS-266</a>] -         Add all tests to the Qunit Test Suite index Page
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-267'>AGJS-267</a>] -         Update sock.js cdn for examples that use it
</li>
</ul>
    
<h2>        Epic
</h2>
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-260'>AGJS-260</a>] -         Implement JavaScript Client Sync Engine
</li>
</ul>
    
<h2>        Feature Request
</h2>
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-70'>AGJS-70</a>] -         Remove jQuery requirement
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-124'>AGJS-124</a>] -         Create a Diff Sync Client
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-268'>AGJS-268</a>] -         Move Sync Server js-client example to cookbook
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-279'>AGJS-279</a>] -         Merge AeroGear.ajax into UnifiesPush Client
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-281'>AGJS-281</a>] -         Deprecate Notifier
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-287'>AGJS-287</a>] -         Update Sync Lib to not use &quot;Array Pattern&quot;
</li>
</ul>
                                        
<h2>        Task
</h2>
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-257'>AGJS-257</a>] -         Remove ES6 polyfil
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-269'>AGJS-269</a>] -         Deprecate Authorization module
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-270'>AGJS-270</a>] -         Deprecate Google Drive cookbook example
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-275'>AGJS-275</a>] -         JavaScript Client Engine should support JSON Patch
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-280'>AGJS-280</a>] -         Remove Authz and AeroGear.ajax related stuff from builder
</li>
</ul>
                
<h2>        Sub-task
</h2>
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-254'>AGJS-254</a>] -         Make ES6 promise polyfill optional
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-256'>AGJS-256</a>] -         README Library Deps need updating
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-258'>AGJS-258</a>] -         Remove promise polyfill from custom builder
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-259'>AGJS-259</a>] -         Update Dist repo with promise removal
</li>
</ul>
    
