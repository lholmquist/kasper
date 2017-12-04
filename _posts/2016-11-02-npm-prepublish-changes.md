---
layout: post
title:  "NPM Prepublish changes"
date:   2016-11-02
permalink: npm-prepublish-changes
categories: Node.js NPM 
---

NPM 4 was released not long ago, about 2 weeks, and with it came some changes. Some breaking, some not, but there is an interesting deprecation that happened with regards to the `prepublish` script.

Currenlty, if you had a prepublish entry in your package.json

	{
    	scripts: {
        	prepublish: "nsp check"
        }
    }
    
This would get run whenever you did a `npm publish`.  Which is probably what you would expect with a name like prepublish.

Another thing was also happening though.  When you ran `npm install` with no arguments, the prebublish step would still be run.  Which is not what you would expect.

Example:

	$ ~/publish-change » npm install                                                                                                

	> publish-change@0.0.1 prepublish
	> nsp check

	(+) No known vulnerabilities found

So a few things seem to be happening in NPM 4.

First, there is now a new lifecycle script called `prepare`, which runs during `npm install`.

	{
    	scripts: {
        	prepare: "nsp check"
        }
    }

Using our previous example:


	$ ~/publish-change » npm4 install                                                                                                

	> publish-change@0.0.1 prepare
	> nsp check

	(+) No known vulnerabilities found


**note: there is no npm4 command, i'm just using that to illustrate me running different versions**

Second, there is now a temporary `prepublishOnly` lifecycle script.  This will only run during a `npm publish`

Third, `prepublish` has been deprecated, but not the whole thing.  I know, this is the interesting part.  The part of `prepublish` that ran during the those no argument npm installs is the part that is deprecated,  that part is now actually `npm prepare`.

This is the warning message produced when running `npm prepublish` in NPM 4:


	npm WARN prepublish-on-install As of npm@5, `prepublish` scripts will run only for `npm publish`.
    
	npm WARN prepublish-on-install (In npm@4 and previous versions, it also runs for `npm install`.)
    
	npm WARN prepublish-on-install See the deprecation note in `npm help scripts` for more information.
    
    
So if you are relying on your prepublish scripts to do anything for you during an npm install, you might want to update to using the `prepare` and `prepublishOnly` scripts for the time being.

At some point in the future,  `prepublishOnly` will be removed and you can go back to using just `prepublish` and `prepare`(if needed).


![](https://ssl-forum-files.fobby.net/forum_attachments/0016/9838/lolcat_what.jpg)
