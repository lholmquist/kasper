---
layout: post
title:  "AeroGear JS 1.4.0 Release"
date:   2014-03-19
permalink: aerogear-js-1-4-0-release
categories: releases aerogear.js AeroGear 
---

The next release, version 1.4.0, of the AeroGear.js library has been released.

This release a little for everyone.  A new Adapter, a new Cookbook, and some code deprecation

### Deprecation Notice

Back in 1.3.0 we deprecated the synchronous api for DataManager to better fall inline with the async nature of the IndexedDB and WebSQL adapters.

However, we added an option to allow users to keep using the sync api for the Memory and SessionLocal adapters.

In this release we removed the sync option.  So don't use it, please :)

### Cookbook

We've added a cookbook with some initial examples.

Each example is a small web application and node.js server( if needed ) that you can run to discover a use case for some of the AeroGear.js library features.

More recipies will be added in the future, so make sure to keep an eye out.

Also,  if there is a recipe you would like to see or contribute, please file a JIRA here: 
[AeroGear.js JIRA Project](https://issues.jboss.org/browse/AGJS)

[Cookbook on Github](https://github.com/aerogear/aerogear-js-cookbook)

### OAuth2 Adapter

This release sees a new module and _experimental_ adapter added to the library.

We've added an **Authorization Module**, with an OAuth2 **Adapter**.

This adapter is best used when "plugged" in to the pipeline module.

It will allow your pipes to get access to services that are protected by OAuth2, such as Google Drive.

For an example of how to use this new adapter,  checkout the cookbook example, [Google Drive](https://github.com/lholmquist/aerogear-js-cookbook)

### Code

As always, you can view the source code [here](https://github.com/aerogear/aerogear-js)

If bower is your thing:

    $ bower install aerogear

Or if you want to create a custom build, [JS Custom Builder](http://aerogear.org/download/custom/)


#### Release Notes - AeroGear JavaScript - Version 1.4.0
    
#### Bug

<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-128'>AGJS-128</a>] -         STOMP notifier unsubscribe method fails with &quot;Cannot unsubscribe as no subscription exists for id undefined&quot;
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-136'>AGJS-136</a>] -         DataManager WebSQL is vulnerable to SQLI attack
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-140'>AGJS-140</a>] -         WebSQL, &quot;-&quot; database name causes an error 
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-145'>AGJS-145</a>] -         DataManager doesn&#39;t load in chrome packaged app environment
</li>
</ul>
                            
#### Feature Request
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-19'>AGJS-19</a>] -         Create another server side offering for getting started guide
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-72'>AGJS-72</a>] -         Add OAuth2 Adapter
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-104'>AGJS-104</a>] -         Create a AeroGear JavaScript Cookbook
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-119'>AGJS-119</a>] -         OAuth2 adapter - add Some Non standard options
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-127'>AGJS-127</a>] -         Add debug property support for STOMP over websocket notifier
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-129'>AGJS-129</a>] -         Find handler by address in Vert.x notifier unsubscribe if callback is not provided
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-133'>AGJS-133</a>] -         Bower repos for custom builds
</li>
</ul>
                                        
#### Task
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-110'>AGJS-110</a>] -         Add Docs For OAuth2 Adapter
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-144'>AGJS-144</a>] -         Remove Deprecated code
</li>
</ul>
                
#### Sub-task
<ul>
<li>[<a href='https://issues.jboss.org/browse/AGJS-111'>AGJS-111</a>] -         OAuth2 adapter recipe
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-112'>AGJS-112</a>] -         Create A Pipeline Recipe
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-113'>AGJS-113</a>] -         DataManager Recipe
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-114'>AGJS-114</a>] -         Authenticator Recipe
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-117'>AGJS-117</a>] -         UnifiedPush Recipe
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-118'>AGJS-118</a>] -         SimplePush Recipe
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-120'>AGJS-120</a>] -         Add Validate Token endpoint option
</li>
<li>[<a href='https://issues.jboss.org/browse/AGJS-121'>AGJS-121</a>] -         Add &quot;Scopes&quot; parsing option
</li>
</ul>
    
