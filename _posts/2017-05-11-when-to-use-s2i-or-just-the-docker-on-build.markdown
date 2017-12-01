---
layout: post
title:  "Dockerizing Node.js Apps"
date:   2017-02-27
disqus: true
permalink: when-to-use-s2i-or-just-the-docker-on-build
categories: [Node.js, nodeshift, openshift, node, source-to-image, s2i]
---

I like Node.js and I like Docker.  While I am not an expert on either, I do pretend to be one at work.

Lately I've been taking a look at [Openshift CDK](https://developers.redhat.com/products/cdk/overview/) and how i can develop Node.js apps against it.  Specificly, I was looking at the [MSA Hello World Demo and the Bounjour microservice](https://cdn.rawgit.com/redhat-helloworld-msa/helloworld-msa/master/readme.html#_deploy_bonjour_nodejs_microservice).

[I also recently wrote about setting up that CDK environment on a freshly re-installed MacBook Pro.](http://blog.lholmquist.org/installing-openshift-cdk-on-my-newly-re-installed-macbook-pro/).  I would check it out, it's some good writing.

My initial goal was to figure out how to "Dockerize" a Node.js application and then put it on my local openshift vm, but when i started to look at little deeper, i found a few different ways of doing it(Dockerizing).  So hopefully this post will go into the different ways.

### Basic Node App

Before i started with my Dockerization, I created a basic Node.js Web App using express.  Here is a [repo with the app](https://github.com/lholmquist/node-web-app)

{% highlight javascript %}
'use strict';

const express = require('express');

// Constants
const PORT = 8080;

// App
const app = express();
app.get('/', function (req, res) {
  res.send('Hello world\n');
});

app.listen(PORT);
console.log('Running on http://localhost:' + PORT);
{% endhighlight %}

Of course you will need to do an `npm install express --save` to get express.

Then a `npm run start` will start it up.

If you taken any intro to Node tutorial this should be very familiar. In fact, the example code is taken from the Nodejs.org guide for dockerizing web apps, you can check that out here:  (https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)[https://nodejs.org/en/docs/guides/nodejs-docker-webapp/]

Or you can read a little more here, since i do that dockerized app in this post.

#### Basic Dockerized Node App

Making this code into a basic docker image(Dockerizing) is fairly simply.  We will need to create a Dockerfile.

_note: This is taken from the official Node.js docs,  The link is above_

First thing, we need a base image to work from, so lets just choose the official node.js image

{% highlight javascript %}
FROM node:6.9.5
{% endhighlight %}

Now we need a directory inside our image to put our code.

{% highlight javascript %}
//Create app directory
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
{% endhighlight %}

And then we need to copy our package.json and our code and run npm install

{% highlight javascript %}
//Install app dependencies
COPY package.json /usr/src/app/

Bundle app source
COPY . /usr/src/app

RUN npm install
{% endhighlight %}

Then we need to expose port 8080 so we can get access to our server

{% highlight javascript %}
EXPOSE 8080
{% endhighlight %}

Then finally we specifiy a command to run when we finally run the image.

{% highlight javascript %}
CMD [ "npm", "start" ]
{% endhighlight %}

Now we just need to build this image:

{% highlight javascript %}
docker build -t lholmquist/docker-web-app .
{% endhighlight %}

We use the docker build command with the `-t` flag so we can tag it with the name `lholmquist/docker-web-app` and the `.` is telling docker build to use our current directory

Running a `docker image list` should output something similiar:

![docker-image](/content/images/2017/Feb/Screen_Shot_2017_02_22_at_10_51_10_AM.png)


Now, that is only an image, so to run it, you can do something like this:

{% highlight javascript %}
docker run -p 8080:8080 lholmquist/docker-web-app
{% endhighlight %}

And you should see something similiar in your console:

![](/content/images/2017/Feb/Screen_Shot_2017_02_22_at_10_53_26_AM.png)

And if you go to [http://localhost:8080](http://localhost:8080) in a browser, you should see something.


### Using Source-to-image

If you don't want to deal with Dockerfiles then you could use the [Source-to-image(S2I) tool from Openshift](https://github.com/openshift/source-to-image).

S2I will basically take your code and a compatible S2I image and smash them together to give you a nice container to run.

So how does this work, it sort of sounds like magic.  Well, it sort of is.

Lets take a look at the command we are going to use to build our apps docker image.  For an example app, we will keep using the basic express example from earlier. This repo https://github.com/lholmquist/s2i-web-app, will have the code.  So clone that and be in that directory.

`s2i build . bucharestgold/centos7-s2i-nodejs:7.5.0  s2i-web-app`

Lets disect this.

`s2i build` - We are using the build command of the s2i cli tool.

The `.` is telling the tool that we are using our current directories code.

This next part is interesting:

`bucharestgold/centos7-s2i-nodejs:7.5.0`.

Lets look at this from back to front.

`7.5.0` is the node version this image uses.

`centos7-s2i-nodejs` is the image we are going to use. I'll explain in a minute why we used this one.

`bucharestgold` is just the namespace from dockerhub.

So if run this command, You will see the base image(the centos7-s2i-nodejs) download like a normal docker build would.

Then you will see a message:

{% highlight javascript %}
---> Installing application source
{% endhighlight %}

What this is doing is copying your code in your current directory to a directory in the image.

Then

{% highlight javascript %}
---> Building your Node application from source
{% endhighlight %}

This basically is just running a `npm install -d`

Once done, you can then run the similar `docker run` command we used earlier to create a running container.

{% highlight javascript %}
docker run -p 8080:8080 s2i-web-app
{% endhighlight %}



And if you go to [http://localhost:8080](http://localhost:8080) in a browser, you should see something just like the other example.



So the reason we used this image, is becuase it is already "enabled" to use s2i's features.

Specifically, there is a `.s2i` directory built into the image, that holds some scripts.  One of those scripts is the "assemble" script, you can see the contents here: https://github.com/bucharest-gold/origin-s2i-nodejs/blob/master/s2i/assemble

When we do a `docker build` these steps will run.  As we saw above, it copies our code and runs `npm install`

Now this can be used by itself for getting a Node.js app(or really any other language app) into docker quickly,  but where this tool shines is when developing Node.js apps for Openshift.

If we take a look at the base images Dockerfile, we can see that actually we [drop the root user and change the permissions](https://github.com/bucharest-gold/origin-s2i-nodejs/blob/master/Dockerfile#L67).  This is becuase, on Openshift, you cannot run process as root.  So creating the docker container this way, saves some steps when developing with openshift.

We also provide some "ONBUILD" docker images incase you are in a scenario were you want to use this `bucharestgold/centos7-s2i-nodejs` image and need to use a Dockerfile.

You would just put this in your "FROM" part of your Dockerfile

{% highlight javascript %}
FROM bucharestgold/centos7-s2i-nodejs:7.5.0
{% endhighlight %}

Lets actually take a look at what that original Dockerfile would look like, if we used this image instead.


Here is the original:

{% highlight shell %}
    FROM node:6.9.5

  # Create app directory
  RUN mkdir -p /usr/src/app
  WORKDIR /usr/src/app


  # Install app dependencies
  COPY package.json /usr/src/app/

  # Bundle app source
  COPY . /usr/src/app

  RUN npm install

  EXPOSE 8080

  CMD [ "npm", "start" ]
{% endhighlight %}


Now lets see what it would look like if we used the "ONBUILD" image,

{% highlight javascript %}
FROM bucharestgold/centos7-s2i-nodejs:7.5.0
{% endhighlight %}


Yup,  thats it.  When we run a `docker build`, our base image takes care of the copying and installing, and will set the `CMD`.


#### Conclusion

As we saw, there are a few different ways of Dockerizing our Node.js apps,  The best way really depends on your scenario, this post is just showing that there are different ways.

If you want to learn more about S2I and the work we are doing related to Node.js check out our [Bucharest-gold repo](https://github.com/bucharest-gold/origin-s2i-nodejs).


And Now the cat picture:

![](http://www.lolcats.com/images/u/08/16/lolcatsdotcomdhecrixlsti39u50.jpg)
