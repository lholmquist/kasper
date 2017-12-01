---
layout: post
title:  "Node Core Modules Exploration - DNS"
date:   2016-12-01
permalink: node-core-modules-exploration-dns
categories: Node.js dns 
---

Last time we checked out the [Debugger Module](http://blog.lholmquist.org/node-core-modules-exploration-debugger/),  this time we check out the DNS module.

First question, what is DNS.  Well according to a google search i did:

	Domain Name Servers (DNS) are the Internet's equivalent of a phone book. They maintain a directory of domain names and translate them to Internet Protocol (IP) addresses. This is necessary because, although domain names are easy for people to remember, computers or machines, access websites based on IP addresses.
    
    
So, spoiler alert, the DNS Module helps to do that.

This isn't going to be a post on network programming, sorry.

#### 2 Categories

So the functions that make of this module are actually broken up into 2 categories and the difference between the 2 are how dns lookups are made.

The first category uses the underlying operating systems way of doing things related to DNS. **Network communications may or not be performed here, it's up your OS** There is actually only 1 function here,  `dns.lookup`.

It is recommend that if you are developing something and want it to work the same way as other applications on your OS that do DNS'y type things,  use this.

The second category of functions will always use the network to perform their DNS queries.  Basically all the functions expect `dns.lookup` are included here.
They also don't use the same config files that `dns.lookup` might use, for example, `/etc/hosts`.


There are some subtle differences between the 2 choices, which is explained in more detail in the [official docs](https://nodejs.org/dist/latest-v7.x/docs/api/dns.html#dns_implementation_considerations)


#### dns.lookup

So there is something interesting that the `dns.lookup` function does.  If you look at the function signature, we see it requires a callback, which means it is async.

	dns.lookup(hostname[, options], callback)
    
    
But it is only async from Javascripts perspective,  it actually makes calls to [getaddrinfo](http://man7.org/linux/man-pages/man3/getaddrinfo.3.html) syncrhonously.  It has something to do with running on libuv's threadpool which is much more computer sciency than i like to get.


### Examples

So enough of the technical stuff, lets see some code and examples of why you would want to use this module.

Using this blogs address as an example, lets see how some of these resolve functions work.

First lets get the IP address using the `dns.resolve4` function:

	const dns = require('dns');

	dns.resolve4('blog.lholmquist.org', (err, addresses) => {
    	console.log(addresses);
	});


This returns an array with the value: `[ '54.172.97.196' ]`


We can also find the CNAME record.

	"CNAME is A Canonical Name record (abbreviated as CNAME record) is a type of resource record in the Domain Name System (DNS) used to specify that a domain name is an alias for another domain, which is the "canonical" domain."
    
Our code very similiar to the above:

	const dns = require('dns');

	dns.resolveCname('blog.lholmquist.org', (err, addresses) => {
    	console.log(addresses);
	});


This time we get : `[ 'ghostblog-lholmqui.rhcloud.com' ]`

Thats right folks,  this blog is running on Openshift!

Now here is an interesting thing, since we have the IP address we can use the `dns.reverse` function to find the hostname.  

	const dns = require('dns');

	dns.reverse('54.172.97.196', (err, addresses) => {
    	console.log(addresses);
	});

Something intersting is returned: `[ 'ec2-54-172-97-196.compute-1.amazonaws.com' ]`

Thats right, Openshift is using AWS behind the scenes.

Ok, last one.  Lets check out what name servers i'm using.  Now for this, we use a slightly different hostname, since blog.lholmquist.org is a sub domain.

	dns.resolveNs('lholmquist.org', (err, addresses) => 	{
    	console.log(addresses);
	});
    
This gives us:

	[ 'c.ns.zerigo.net',
	  'd.ns.zerigo.net',
	  'e.ns.zerigo.net',
	  'a.ns.zerigo.net',
	  'b.ns.zerigo.net' ]
      
Showing that i use zerigo for my name server.


And now,  a Cat Picture

![](http://memeburn.sndytsvoxozgokstuvcm.netdna-cdn.com/wp-content/uploads/2012/06/icann-haz-domain-name-lolcat.jpg)
