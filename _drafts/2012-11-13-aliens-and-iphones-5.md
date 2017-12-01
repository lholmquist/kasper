---
layout: post
title: "Aliens and iPhones"
tagline: Huh?  part 5
description: "Show how to test mobile with Arquillian"
categories: [mobile, testing]
tags: [Arquillian, JBoss, iOS]
published: true
---


## Last Time

Previously on [Blog]({{ BASE_PATH }}{{ page.previous.url }}),  we setup the our environment to run our tests on the iPhone Simulator

This time we will see how to run the device on our actual iPhone.


## Device Test

In order to do this, you need to be an iOS developer, so you can install things on your device.  Also, you need to be on the same Wifi network as you machine.

If you meet that requirement, then navigate to the selenium svn repo that you checked out.  Go to the iphone directory and open the iWebdriver xcode project in , ummm , Xcode.

Before we install this on the phone,  you can click run, with an iphone simulator selected and just test out that it will build correctly.

Now, attach you phone,  if you click the run now,  there should be errors.  we need to modify a couple things so it will run on the phone

First, we need to change the code sign values, screenshot:

![iWebdriverBuildSettings](/img/iWebdriverBuildSettings.png)

Then, we need to change the "Valid Architectures" from i386 to armv7, screenshot:

![iWebdriverArchSettings](/img/iWebdriverArchSettings.png)


Now when you run this, it should build and then install on your phone.


When it starts up, there will be a "http" address on the bottom of the app,  this is important.  We need to add this address to our arquillian.xml file in the webdriver section:

    <extension qualifier="webdriver">
        <property name="browserCapabilities">iphone</property>
        <!-- Remote location aka, where it's running on your device-->
        <property name="remoteAddress">http://192.168.1.4:3001/wd/hub/</property>
    </extension>


Next, Since we are running webdriver on our device we can skip a couple processes, so add another property to the ios-drone qualifier

    <property name="skip">true</property>

This can also be added to the ios qualifier, if you also added that.

Also, make sure you have bound your JBoss instances to your IP Address:

    $ ./standalone.sh -b youIPAddress


Now, with iWebDriver running on your iPhone, run the test.  You should see the browser being automated.  Again, if you are running in an IDE, set a break point.


Yea, i know,  i was excited too!!

Here is the code up to this point: [Tag 0.0.5.drone](https://github.com/lholmquist/as-quickstarts/archive/0.0.5.drone.zip)

Here is my forked repo: [Drone Branch](https://github.com/lholmquist/as-quickstarts/tree/drone)


##Next Up

Well, there are a few things that could be covered.

* Make it easier to run different platform tests
* A more complex example
* Run on an Android device, probably not since i don't heart android, and i only have an old android device
* Anything that my fans want to see.  hint, hint,  someone please use the comment section,  yes, even you trolls.


![lolcat](https://i.chzbgr.com/completestore/12/6/21/qTQFY-3e0U-7pEIxWqRFMQ2.png)



