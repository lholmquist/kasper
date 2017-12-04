---
layout: post
title:  "Node.js Keycloak Admin REST Client"
date:   2016-04-27
permalink: node-js-keycloak-admin-rest-client
categories: REST Node.js keycloak 
---

Yea, i know the title sounds lame, but the content to follow should be really cool.


So i sort of feel a bit like Troy McClure now.  "You may remember me from other node.js REST clients such as swapi-node and phishin-js"

Thats right folks, i've start to write another [node.js REST client](https://www.npmjs.com/package/keycloak-admin-client), but this time it's for the Keycloak Admin REST Client API's.

Just a quick summary of what [Keycloak](http://keycloak.jboss.org/):

	Integrated SSO and IDM for browser apps and RESTful web services.  Built on top of the OAuth 2.0, Open ID Connect, JSON Web Token (JWT) and SAML 2.0 specifications.  Keycloak has tight integration with a variety of platforms and has a HTTP security proxy service where we don't have tight integration.  Options are to deploy it with an existing app server, as a black-box appliance, or as an Openshift cloud service and/or cartridge.
    
They also have a REST Client for doing Admin-y type things like looking up a realm and other CRUD like operations.

The API docs here, http://keycloak.github.io/docs/rest-api/index.html, defines what we can do.  And yes, there is a shit ton of things.


The docs for the node.js client are here: http://bucharest-gold.github.io/keycloak-admin-client/


If you want to follow along, you will need to have your own Keycloak server running.  There is a `start-server.sh` file in the `build` directory of the [github repo](https://github.com/bucharest-gold/keycloak-admin-client/tree/master/build) that will download the 1.9.2 distribution and start the server with some test data.

Ok now for the example.  Lets start simple here. 

First thing is to define our settings, so lets take a look at what is required.

	let adminClient = require('keycloak-admin-client');
    
    let settings = {
    	baseUrl: 'http://127.0.0.1:8080/auth',
        username: 'admin',
        password: 'admin',
        grant_type: 'password',
        client_id: 'admin-cli
	}
    
so baseUrl is the url to our keycloak server, and username and password is... seriously, do i need to explain what those mean?

don't worry to much about grant_type and the client_id of admin-cli is actually setup by default for all new keycloak setups.  


Once we have our settings, lets call the the adminClient function to get a handle on the client object, so we can call `realms.find` function.

	adminClient(settings)
      .then((client) => {
       ...
      })
      
The adminClient will return a Promise that returns the client object.  The client object will have all the methods available for us to call.

During the call to adminClient, we are actually authenticating against the Keycloak server and getting an access token to make further requests.

now for the call to get our realms

	client.realms.find()
      .then((realms) => {
        console.log(realms)
      })

`client.realms.find` will also return a Promise with the list of realms.


So all of that together(with a catch handler):

	let adminClient = require('keycloak-admin-client');
    
    let settings = {
    	baseUrl: 'http://127.0.0.1:8080/auth',
        username: 'admin',
        password: 'admin',
        grant_type: 'password',
        client_id: 'admin-cli
	};
    
    adminClient(settings)
   	  .then((client) => {
        client.realms.find()
          .then((realms) => {
            console.log(realms); // [{...},{...}]
          });
      })
      .catch((err) => {
        console.log(err);
      });
      

**Currently as of this writing(04/27/2016) I've implemeted CRUD operations for Realms and Users.  But more are coming soon**

**Update 05/04/2016: Updated the API usage for 0.2.0**

And as always, a cat picture

![lolcat](http://www.enews.org/blog/_pics/lolcat.jpg)
