---
layout: post
title: "Aliens and iPhones"
tagline: Huh?  part 4
description: "Show how to test mobile with Arquillian"
categories: [mobile, testing]
tags: [Arquillian, JBoss, iOS]
published: true
---


## Last Time

Previously on [Blog]({{ BASE_PATH }}{{ page.previous.url }}),  we setup our environment for running tests on android and also refactored the test a little bit.

This time we will use the just released iOS Arquillian extension to simulate our test on an iPhone.


## iOS Setup

To run the tests, you are going to need to have xcode installed, so you are going to need a Mac.  I'm using OS X 10.8 with Xcode 4.5.2, command line tools installed.  You can download xcode from the Mac App store.

I'm going to assume you can figure out how to setup xcode and stuff.

## Arquillian Setup

First thing that we need to do is add the dependency for the ios extension to our pom.xml

    <dependency>
        <groupId>org.jboss.arquillian.extension</groupId>
        <artifactId>arquillian-ios-depchain</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <type>pom</type>
        <scope>test</scope>
    </dependency>

Since this is not yet in maven central, there are 2 ways of getting the dependency resolved.

1.  clone the [repo](https://github.com/arquillian/arquillian-extension-ios) and run:

        mvn clean install

2.  Edit your settings.xml file and add in the jboss public repo



Now we need to modify our Arquillian.xml file, and add some specific iOS settings.

First is the thing that will call our iPhone simulator:

    <extension qualifier="ios">
    <!--
        this is optional - the extension can do `git clone` and build
        the project automatically, but it can take a long time
    -->
    <property name="waxsimBinary">path to your WaxSim binary</property>
    </extension>

I actually left the above config out.


Next, we need the iWebdriver app that runs on our Simulator

    <extension qualifier="ios-drone">
        <!--
            this is optional - the extension can do svn checkout automatically,
            but it can take a long time
        -->
        <property name="localSeleniumCopy">path/to/selenium-trunk/</property>
    </extension>

I left this in,  it does take a long time. You can download the source from here:

    $ svn checkout http://selenium.googlecode.com/svn/trunk/

On the first run of the test, the above will build,  which could take a minute or two.

And don't forget to change to browserCapabitlies property to iphone

    <extension qualifier="webdriver">
        <!-- this is optional if you set -->
        <property name="browserCapabilities">iphone</property>
    </extension>

Almost there,  just hang on.

In our test, we need to add one thing in the if statement

    if( GrapheneContext.holdsInstanceOf(AndroidDriver.class) || GrapheneContext.holdsInstanceOf(IPhoneDriver.class) ) {


Now, if we run the test, command line or IDE, it should automate the test on the iPhone simulator.  If you are using the IDE to run your tests, you can put a break point in and debug to see that the simulator pops up.

So, yea, that is pretty cool.

Here is the code up to this point: [Tag 0.0.4.drone](https://github.com/lholmquist/as-quickstarts/archive/0.0.4.drone.zip)

Here is my forked repo: [Drone Branch](https://github.com/lholmquist/as-quickstarts/tree/drone)


##Next Up

Well what about actually testing on the device itself.


[Beam Me Up Scotty... To the Next Post]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://i.chzbgr.com/completestore/2008/5/7/aliencatexits128546748960791250.jpg)




