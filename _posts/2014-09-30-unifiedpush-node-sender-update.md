---
layout: post
title:  "UnifiedPush Node Sender - Update"
date:   2014-09-30
permalink: unifiedpush-node-sender-update
categories: AeroGear Node.js UnifiedPush 
---

There has been a slight update to the AeroGear UnifiedPush Server node.js sender.

Previously, when requiring the package, you needed to use the "AeroGear" namespace.

	var sender = require('unifiedpush-node-sender').AeroGear;
    
    
Well, that didn't really feel good.  So now with this update, [version 0.7.0](https://www.npmjs.org/package/unifiedpush-node-sender) you can now just do this:

    var sender = require('unifiedpush-node-sender');
    
    


![lolcat](http://www.enews.org/blog/_pics/lolcat.jpg)
