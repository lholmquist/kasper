---
layout: post
title: "Aliens and iPhones"
tagline: Huh?  part 3
description: "Show how to test mobile with Arquillian"
categories: [mobile, testing]
tags: [Arquillian, JBoss, Android]
published: true
---



## Last Time

Previously on [Blog]({{ BASE_PATH }}{{ page.previous.url }}), we refactored our code a little and automated the member registration process using Arquillian Drone and Graphene.

As promised last post,  this one will deal with testing on a mobile device.  We will start an Android device first(Emulator).


## Android Setup

First thing we need to do is make sure we have a the Android SDK all setup.  [Guide here](http://developer.android.com/sdk/index.html)

*We are going to be testing on Android 4.1, so you might want to make sure that version is installed*


Also download the Android-server.apk [here](http://code.google.com/p/selenium/downloads/list).  We will be using version 2.21.0.apk

## Arquillian Setup

There is a little bit of setup we need to do for this to work.

First, we need to add the dependencies to our pom.xml .  I actually added a new profile, and put the dependencies under that.  Just remember to select that profile when running the android test.

    <profile>
        <id>arq-android</id>
            <dependencies>
                <dependency>
                    <groupId>org.jboss.arquillian.extension</groupId>
                    <artifactId>arquillian-android-depchain</artifactId>
                    <version>1.0.0.Alpha1</version>
                    <type>pom</type>
                    <scope>test</scope>
                </dependency>
                <dependency>
                    <groupId>org.seleniumhq.selenium</groupId>
                    <artifactId>selenium-android-driver</artifactId>
                </dependency>
                <dependency>
                    <groupId>org.seleniumhq.selenium</groupId>
                    <artifactId>selenium-remote-driver</artifactId>
                </dependency>
            </dependencies>
        </profile>



Now with the dependecies out of the way, lets move to the arquillian.xml file.

First we need to add the android config:

    <extension qualifier="android">
        <!--Path to your Android Home,  can also use the ANDROID_HOME if set-->
        <property name="home">/path/to/android/home</property>
        <property name="avdName">Android_4.1</property>
    </extension>

This little piece of config will tell Arquillian where the android sdk on your machine.  I'm doing this on OS X and ~/path/to/thing   didn't work,  so make sure you do the full path.

The next piece of config is for the android drone:

    <extension qualifier="android-drone">
        <!-- path to the android server spk  from http://code.google.com/p/selenium/downloads/list-->
        <property name="androidServerApk">/path/to/android-server-2.21.0.apk</property>
    </extension>


You might remember that we add this config last time:

     <extension qualifier="webdriver">
        <property name="browserCapabilities">firefox</property>
    </extension>

If we still want to run the firefox version, then keep this the same and make sure the android profile isn't selected.

If we want to run the android version, then change this to specify android

     <extension qualifier="webdriver">
        <property name="browserCapabilities">android</property>
    </extension>

and make sure the android maven profile is selected.

I know, I know, there's probably another way to make this switch much easier.  I admit i didn't look into it.  Thats what the comment section is for. For others to drop some knowledge


The last little bit of setup will be in index.html.  We need to add an id to one of the buttons,  it's roughly on line 222:

    <!-- Footer -->
        <div class="footer" data-role="footer" data-position="fixed">
            <div class="footer_left">
                <a href="#intro-art" data-role="button" data-icon="home">Home</a>
            </div>
            <div class="footer_right">
                <span class="footer_txt">Member:</span>
                <a  id="addMember" href="#register-art" data-role="button" data-inline="true" data-icon="plus">Add</a>
                <a href="#member-art" data-role="button" data-inline="true" data-icon="grid">List</a>
            </div>
        </div>
    <!-- /Footer -->


## Refactor Redux

OK, back to the test. First we are going to add a few Annotations to make things easier:

    @FindBy(id = "name")
    WebElement nameField;

    @FindBy(id = "email")
    WebElement emailField;

    @FindBy(id = "phoneNumber")
    WebElement phoneNumberField;

    @FindBy(id = "register")
    WebElement registerButton;

    @FindBy(id = "addMember")
    WebElement addMemberButton;


This basically does the same thing as

    driver.findElement(......)

Now in our test method we need to add this:

    if( GrapheneContext.holdsInstanceOf(AndroidDriver.class) ) {
           waitModel().withMessage("Add button is not present.")
                   .until(element(addMemberButton).isVisible());

           addMemberButton.click();
       }

This will determine if it's an android test we are doing.  Since it is a smaller screen, the UI will be different,  so we need to wait until it loads and the add member button is visible before we click on it.


Once that is done, the next piece we add is:

     waitModel().withMessage("Registration screen is not present.")
               .until(element(nameField).isVisible());

Again we are waiting for the screen to come up. We don't want to prematurely start typing.

The next bit should be familiar from last time,  it's just a little clean up now that we are using annotations:

    nameField.sendKeys("Luke");
    emailField.sendKeys("luke@luke.com");
    phoneNumberField.sendKeys("1234567890");
    registerButton.submit();


The rest of the test is the same,  we wait for the response to come back.


Since the android extension is in Alpha,  things could change and also get better.

Now we can run this test like the other ones.  *have your AVD running first, it helped me.  And Make sure you specify an amount for the SD card*


*Also,  and this is big,  when running your instance of jboss,  use the -b flag to bind to your ip address*

    $ ./standalone.sh -b 123.45.67.890

What we should see is the "Webdriver" load on the emulator,  then Arquillian will deploy the archive to JBoss, then the Emulator should navigate to the page,  and you should see the emulation happening.


Here is the code up to this point: [Tag 0.0.3.drone](https://github.com/lholmquist/as-quickstarts/archive/0.0.3.drone.zip)

Here is my forked repo: [Drone Branch](https://github.com/lholmquist/as-quickstarts/tree/drone)



##Next Up

With Android out of the way, it's time to move on to iOS.  This was just released a few weeks ago,  so it's time to dive in.


[Beam Me Up Scotty... To the Next Post]({{ BASE_PATH }}{{ page.next.url }})


![lolcat](http://2.bp.blogspot.com/-VFGdGM3CuCE/TcjIIwMpHsI/AAAAAAAAM9A/U8OMiN4TSM4/s1600/funny-pictures-halp-alien-tentickles.jpg)

