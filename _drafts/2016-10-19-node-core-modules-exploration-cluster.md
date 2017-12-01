---
layout: post
title:  "Node Core Modules Exploration - Cluster"
date:   2016-10-19
permalink: node-core-modules-exploration-cluster
categories: Node.js 
---

[Previously on](http://blog.lholmquist.org/node-core-modules-exploration-child-processes/) on blog, i went into an overview of the Child Processes module.  This time, we talk about about something that is sort of related to `child_process.fork`, Clusters.

In fact, looking at the [code for Clusters](https://github.com/nodejs/node/blob/master/lib/cluster.js#L6), `child_process.fork` is actually required.

So what is the cluster thing anyway. Well Node.js is single threaded.  So to take advantage of computers that have multiple cores(which is basically everything now), you can use the cluster module to create child processes that share server ports.

For example, lets take a look at a clustered http server.(this is taken from the [official docs](https://nodejs.org/dist/latest-v6.x/docs/api/cluster.html#cluster_cluster))

	'use strict';

	const cluster = require('cluster');
	const http = require('http');
	const numCPUs = require('os').cpus().length;

    if (cluster.isMaster) {
      // Fork workers.
      for (var i = 0; i < numCPUs; i++) {
        cluster.fork();
      }
    
      cluster.on('exit', (worker, code, signal) => {
        console.log(`worker ${worker.process.pid} died`);
      });
    
      cluster.on('online', (worker) => {
      console.log(`Master: ${process.pid}, worker          ${worker.process.pid} is online`);
      });
    } else {
    
      // Workers can share any TCP connection
      // In this case it is an HTTP server
      http.createServer((req, res) => {
        res.writeHead(200);
        res.end('hello world\n');
    
        console.log(`Child Cluster ID
        ${cluster.worker.process.pid}`);
      }).listen(8000);
    }


Here, the cluster is sharing port 8000.  Running this on my Late 2012 Macbook Pro, i get something like this when run:

	Master: 72645, worker 72646 is online
	Master: 72645, worker 72647 is online
	Master: 72645, worker 72648 is online
	Master: 72645, worker 72649 is online


I can kill any of those worker clusters without affecting the others. 

#### How to cluster

So how do we exactly create a new cluster.  We can see from the example above that we just have to call the `cluster.fork` method from the master process.  This will spawn a new worker process.

#### The Worker Class

The Worker class is the main class in the cluster module.  It contains all the public info and methods about a worker.

There are 2 ways to get a handle on it.  If you are in the Master process, you can just call `cluster.workers`

If you are in a worker(not the master process), then just use `cluster.worker`.

Speaking of the Master process.  How do we know that we are in the Master process? We use the `isMaster` method from cluster

	cluster.isMaster
    
You might be able to determine what this might return if it is the master cluster.


Also, all workers that are returned are just an instance of a Child Process.

#### Distribution of connections

You may be wondering how the cluster module determines how to distribute the incoming connections.  There are 2 ways.

The first, which is the default(except for windows, sorry bro), is "Round Robin".  It is also smart enough not to overload any one process.

Just a note that the term "Round Robin" here refers to the programming term, not the [round robin phase](https://en.wikipedia.org/wiki/Supermarket_Sweep#Round_Robin_game) of the awesome Game Show "Supermarket Sweep"

Basically, the master worker, listens and accepts all incoming connections and then distributes them.

The Second, has the master worker creating the listener and givining it to workers that want to use it.  Then those workers accept the incoming connections directly.

To change between the 2, set the `cluster.schedulingPolicy` property with either `cluster.SCHED_RR`(the default on non-windows) or `cluster.SCHED_NONE`.  This must be set before the first worker is spawned, after that, this value is then frozen.

It can all be set using the environment variable `NODE_CLUSTER_SCHED_POLICY` using either `rr` or `none`

It is important to note that there is no shared state between the workers,  so relying heavily on in-memory data for session and login type stuff is not recommended.


#### Next

There are some more sophisticated things that you can do with cluster, so check out the docs.

Next time we will take a look at the [Command Line Options(not really a module, but important, and its next on the list).](http://blog.lholmquist.org/node-core-modules-exploration-command-line-options/)


![](http://wannasmile.com/wp-content/uploads/2012/07/252694229062147106_xmeVPOrF_f.jpg)
