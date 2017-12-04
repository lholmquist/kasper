---
layout: post
title: "Cordova: How does it work?"
description: "Subject Matter Champion thoughts"
tagline:
categories: [Personal, Aerogear]
tags: [Cordova, F2F]
published: false
group: cordova
---


## Intro

Coming up is the Red Hat/JBoss Face to Face.  Each of us on the AeroGear team have a subject to research and become the "Subject Matter Champion".  My Subject is Apache Cordova.  I figured writing a blog post will help me sort out my thoughts.( thanks @qmx ) So here we go....

## Background

For mobile applications, we have basically 3.5 - 4 different "Platforms".

- Mobile Web

- Hybrid

- Hybrid+

- Native

I say there is 3.5 - 4 since the hybrid and hybrid+ could be group together.

Each one has there own pros and cons.  And when it comes to mobile apps, there usually isn't a one size fits all solution.

While you can get the fastest turn for making updates with the Mobile Web than you can with a native app( app store approval and such ), Native gives you the most devices access.  While that is starting to change with new Web API's, i refuse to use the term HTML5 since it is way over used,  just like the term organic,  but i digress,  anyway.  Things are not quite there yet.

If you are a web developer, you have been building your web skills up, but to now make a native app, you must learn a new language.  While that is fun to do,  it can be a big time investment.

This is where the Hybrid "Platform" comes in: Apache Cordova, or Phone Gap.

## Cordova: What is it?

Apache Cordova, is a framework to help leverage your existing web developer skills to create a Native app, that can then be downloaded from an app store/installed on a device.

When i talk about web developer skills, i'm referring to HTML, Javascript and css.  And the devices here that we are talking about are iOS and Android devices.  While cordova supports a bunch more,  we are only going to focus on the 2 i just mentioned.  iOS and Android, for those with "short term" memory loss.  you know who you are ;)

Both Android and iOS have a WebView or UIWebView class that can be used in an app to display web pages without leaving the app you are in.  Think facebook, when you click on some link to an article and he webpage opens,  but you are still in facebook.

While that is a neat thing to do, the Native code can interact with this WebView and the WebView's javascript can also interact with the native code.

What this allows us to do is write javascript, since we are web developers, and interact with device api's that are usually not available to javascript.

"But you said i didn't need to write and native code?"

That's correct.  Cordova is a Wrapper on top of all this stuff that exposes the native device api's through an easy to use javascript api.

How the 2 devices platforms work to interact with each other is a little different,  so on to the next section.

## Cordova: How does it work

Like i said earlier,  Cordova uses the WebView class to display our web content.  Each device platform has a different way of interacting with the native javascript code from the native device code

### iOS

In iOS, the class that the Cordova library builds off of is the UIWebView class.

This method in the UIWebView class is the magic maker:

    - (NSString *)stringByEvaluatingJavaScriptFromString:(NSString *)script

which basically allows the native code to execute javascript.  This is how things are sent back,  but what about sending things to the native code from javascript?

This depends a bit on the version of iOS you are using.  If you are on iOS 4.2 or lower, then cordova uses an iFrame to contact the native code.  It basically, just changes the src attribute in the iFrame and the native code will pick that up.

I'm sort of guessing that this takes care of the iFrame stuff:

    - (BOOL) webView:(UIWebView*)theWebView
             shouldStartLoadWithRequest:(NSURLRequest*)request
             navigationType:(UIWebViewNavigationType)navigationType


while this method gets called with using the below xhr request.  it only gets called once.  since i don't have the iOS 4.2 sim, i can only guess that it will get called again when an iFrame is loaded

If you are on iOS greater than 4.2 then cordova will use xhr requests.  Which is kind of neat.

            execXhr = execXhr || new XMLHttpRequest();
            ......
            execXhr.send(null);

During the init of the CordovaViewController, a CDVURLProtocal, which is a subclass of the URLProtocal class, gets registered with the view, so when ever there is a URL request, this picks it up and does some stuff with it


If the request has nothing to do with cordova, then it will pass through and the native way will take care of it.  And since this is a Webkit based View,  i'm assuming that how ever webkit handles request is what happens here


### Android

By Default, javascript is not enabled in the WebView. So in the setup() method in CordovaWebView.java, it gets enabled

     // Enable JavaScript
        WebSettings settings = this.getSettings();
        settings.setJavaScriptEnabled(true);
        settings.setJavaScriptCanOpenWindowsAutomatically(true);
        settings.setLayoutAlgorithm(LayoutAlgorithm.NORMAL);



But how do we communicate between the javascript and the native code?  The answer depends on the version of android you are using.  If you are on a new version then things tend to be a bit simpler.

There is a method, addJavascriptInterface, on the WebView class that takes an Object, this being the Object with the methods that you want to expose to the javascript, and a String,  which is what you would use to call these methods on the javascript side

    private void exposeJsInterface() {
        .....
        this.addJavascriptInterface(exposedJsApi, "_cordovaNative");
    }


    @JavascriptInterface
    public String exec(String service, String action, String callbackId, String arguments) throws JSONException {
       .........
    }


So then in javascript the cordova code can call this method:

    _cordovaNative.exec( ... );


If this method is not available, then it needs to fallback to the "Prompt" mode.  This is pretty interesting how this works.  The short answer is that it hacks the onJsPrompt method from the WebChromeClient Android class.

If the cordova uses prompt mode,  it basically sends the commands it wants to execute throught a javascript prompt dialog, and then executes code, sends back the result from that execution and makes sure not to show the actual prompt.

     // Calling PluginManager.exec() to call a native service using
        // prompt(this.stringify(args), "gap:"+this.stringify([service, action, callbackId, true]));
        if (reqOk && defaultValue != null && defaultValue.length() > 3
            && defaultValue.substring(0, 4).equals("gap:")) {
            JSONArray array;
            try {
                array = new JSONArray(defaultValue.substring(4));
                String service = array.getString(0);
                String action = array.getString(1);
                String callbackId = array.getString(2);
                String r = this.appView.exposedJsApi.exec(service, action, callbackId, message);
                result.confirm(r == null ? "" : r);
            } catch (JSONException e) {
                e.printStackTrace();
            }
        }

If someone actually tries to use the "Prompt" in javascript for realz, then it just passes through and shows the dialog



## Plugins


One of the cool and important parts about Cordova is that fact that it is pluginable(?), not sure that is a word,  but i like it.

Cordova comes with a bunch of "Core" plugins. Plugins are just api's for accessing the device.  like the camera, or the network info.

You can also write your own if you want.  There are 2 parts to any plugin.


### Javascript

First is the javascript side.  all you need to do is call the cordova.exec() method with some params and thats it. Let take a look at the parameters.

    cordova.exec(function(winParam) {}, function(error) {}, "service",
             "action", ["firstArgument", "secondArgument", 42,
             false]);


_Taken from the cordova docs_

1. function(winParam) {} - Success function callback. Assuming your exec call completes successfully, this function will be invoked (optionally with any parameters you pass back to it)

2. function(error) {} - Error function callback. If the operation does not complete successfully, this function will be invoked (optionally with an error parameter)

3. "service" - The service name to call into on the native side. This will be mapped to a native class.

4. "action" - The action name to call into. This is picked up by the native class receiving the exec call, and, depending on the platform, essentially maps to a class's method.

5. [/* arguments */] - Arguments to get passed into the native environment


### Native Code

Then on the native side,  you create the plugin code that you want to call.

Depending on the platform you are on,  depends on what the code looks like

_Note:  On Android the javascript in the webview doesn't run on the UI thread, it runs on the Web Core thread so to access the UI during a cordova call, some things need to be done _

most platforms have in common that you need to add the plugin you want to use to a config file.

At the moment,  There is no common way for distributing plugins or plugin discovery.

This should be changing soon.

There is this document:  [link](https://github.com/alunny/cordova-plugin-spec) that proposes a plugin spec to follow





