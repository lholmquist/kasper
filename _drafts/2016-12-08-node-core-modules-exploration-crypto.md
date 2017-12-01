---
layout: post
title:  "Node Core Modules Exploration - Crypto"
date:   2016-12-08
permalink: node-core-modules-exploration-crypto
categories: Node.js 
---

Previously, we covered the [DNS module](http://blog.lholmquist.org/node-core-modules-exploration-dns/), now onto a easy subject,  Crypto.

Crypto is a very complex subject and i am nowhere close to an expert or even a beginner on the subject.  So this post won't really go into very much depth on Crypto best practices and such.

So the crypto module is really just an abstraction for the hash, HMAC, cipher, decipher, sign and verify functions of OpenSSL

There is something interesting with the Crypto module that I don't think can happen with the othe Core modules.  You can actually build a version of Node without crypto support!  I know, right

If that is the case, then an error will be thrown when doing `require('crypto')`

Most of the function use some sort of hash algorithm.  To get a list of those that are supported, you can use the `getHashes()` function.  On my system, the results are something like this nice short list:

	[ 'DSA',
	  'DSA-SHA',
	  'DSA-SHA1',
	  'DSA-SHA1-old',
	  'RSA-MD4',
	  'RSA-MD5',
	  'RSA-MDC2',
	  'RSA-RIPEMD160',
	  'RSA-SHA',
	  'RSA-SHA1',
	  'RSA-SHA1-2',
	  'RSA-SHA224',
	  'RSA-SHA256',
	  'RSA-SHA384',
	  'RSA-SHA512',
	  'dsaEncryption',
	  'dsaWithSHA',
	  'dsaWithSHA1',
	  'dss1',
	  'ecdsa-with-SHA1',
	  'md4',
	  'md4WithRSAEncryption',
	  'md5',
	  'md5WithRSAEncryption',
	  'mdc2',
      'mdc2WithRSA',
	  'ripemd',
	  'ripemd160',
	  'ripemd160WithRSA',
	  'rmd160',
	  'sha',
	  'sha1',
	  'sha1WithRSAEncryption',
	  'sha224',
	  'sha224WithRSAEncryption',
	  'sha256',
	  'sha256WithRSAEncryption',
	  'sha384',
	  'sha384WithRSAEncryption',
	  'sha512',
	  'sha512WithRSAEncryption',
	  'shaWithRSAEncryption',
	  'ssl2-md5',
	  'ssl3-md5',
	  'ssl3-sha1',
	  'whirlpool' ]




### Useful things

Here are a couple little useful things.  The first is something you might see when downloading files.  Lots of times there is a file containing the hashes of the files,  this is how to generate those hashes:

	// index.js

	const filename = process.argv[2];
	const crypto = require('crypto');
	const fs = require('fs');

	const hash = crypto.createHash('sha256');

	const input = fs.createReadStream(filename);

	input.on('readable', () => {
    	var data = input.read();
	    if (data) {
	        hash.update(data);
    	} else {
	    	console.log(
        		`${hash.digest('hex')} ${filename}`
            );
    	}
	});
    
    
If we did something like this:

	node index.js ./somefile.js
    
we might get something like this:

	a5338693d7fc1...3267135c somefile.js
    
    
_note:  that example came from the [docs](https://nodejs.org/dist/latest-v7.x/docs/api/crypto.html#crypto_crypto_createhash_algorithm)_


Another little useful thing, could be generating random bytes.

	// Asynchronous
	const crypto = require('crypto');
	crypto.randomBytes(256, (err, buf) => {
	  if (err) throw err;
		  console.log(
          	`${buf.length} bytes of random data:
            	${buf.toString('hex')}`
          );
	});


This will output something like this:  

	256 bytes of random data: 83819415a91ac951e06977cdb48dc83fd9412ed6886b5ae8af43d13a666ead0a21f17276bd46ae671a54beb097076e3ee3d29d7247e84f93897cbd6d6a685d5e6c48180d52bcab7e2184b7d4f6d25a3e2eeccdf2c1df95eea0f021f0ec619ce6dd4a7ab2e41dbbb04fa3a9e75084e55cd88c5f93becbc3144b39bfba5d8a750dc4359d68aa740143d4a29ce0645426949b9eb443bcc772abed493ad29a1de79b1ed9af3299df2212a9b3433362dd57212cb0fb821d64745530b6d5479e49d00c33cc523dd030e3cf2db133f10e4478712e6c1e5acdce357428623c0dc32de3bbbf50b043fe99b9bdb8181f8b013984857816eb0e3fb1c284cffd51a75b9fe0b6
    
    
This is a relatively short post, but as a friend once told me,  "Crypto is hard"  and i'm no crypto expert.

Perhaps this cat picture will make things better :)


![](http://66.media.tumblr.com/8d01e090d5ce08248db8d8e411c9376f/tumblr_odyp6mOOCD1trt7ryo1_500.jpg)

