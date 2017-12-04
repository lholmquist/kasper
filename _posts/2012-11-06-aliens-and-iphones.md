---
layout: post
title: "Aliens and iPhones"
tagline: Huh?  part 1
description: "Show how to test mobile with Arquillian"
categories: [mobile, testing]
tags: [Arquillian, JBoss]
published: true
---


## Intro

For some, testing has become an important part of the development lifecycle.  There are a bunch of popular testing frameworks out there for both client and server.  JBoss is pushing Arquillian, which takes testing to the next level by allowing you to test in a container, thus effectively testing how your code would work on the server it will eventually run on.  Lets call these tests integration tests.

Now, there are numerous articles on [Arquillian.org](Arquillian.org) that show how to get started and what the goals of the project are, so I won't go into all that here.   What i'm really interested in, is how the project relates to a mobile environment.  That great that we can server-side code in our container, for instance JBoss AS 7,  but what about client side?

## Task

There are client libraries that do unit testing, such as qunit,  but what about actual integration tests where we can control the browser.  Some people use [Selenium](http://seleniumhq.org/), which has a suite of different products to help control the browser.  There seems to be a lot of setup.  I just want to integrate this into the tests i already have in my maven build.  How?

You may remember that i mentioned Arquillian, you should it was 1 paragraph ago.  Well, Arquillain has a project called Drone, that basically wraps all the selenium stuff up and is managed by the Arquillian framework.  This should allow me to run these new client integration tests along side my server-side tests.

## Setup

I'll be assuming that this pre reqs have already been met:

    java sdk 6.0+
    maven 3.0+
    IDE of choice, I use IntelliJ
    Git
    JBoss AS 7.
    Firefox //for this part atleast
    Time

*Note:  Some of this stuff we are going to be trying is in Alpha, so expect it to change.  Also there seems to be a decent amount of setup, maven stuff, for Arquillian,  so just be patient*

The project that we will be modifying will be the HTML5 Kitchensink example from AeroGear found [here](https://github.com/aerogear/as-quickstarts) .  Also, I must give credit to [Karel Piwko](https://github.com/kpiwko)  for his awesome presentation at [JavaOne](http://www.myexpospace.com/JavaOne2012/SessionFiles/TUT5039_mp4_5039_001.mp4).  I'll be borrowing some code from him.  Check out his repo [here](https://github.com/kpiwko/html5-demoapp)


First things first,  Lets create a new branch, called drone, to work with so we don't pollute the master branch:

    $ git checkout -b drone

Since this is a maven project, we can easily import this into my IDE.

Just to make sure everything runs ok in the IDE, lets run the provided test with the arq-jbossas-remote maven profile.  *Make sure you have AS 7 running before running it*

It should pass since we haven't modified anything yet.

This would be a good spot to stop and get something to drink, eat, go to the bathroom or whatever.  The next steps could take a while.  I can wait..............


Ok....Now it's time to dive in.

## Start

First thing is to create a new test class.  All i did was copy the current test *MemberRegistrationTest.java* and named the copy *MemberRegistrationClientTest.java*

Then I removed everything except for the @Deployment stuff and add a little something.:

    @RunWith(Arquillian.class)
    public class MemberRegistrationClientTest {
       @Deployment(testable = false) //This is to tell Arquillian we want Client Mode
       public static Archive<?> createTestArchive() {
          return ShrinkWrap.create(WebArchive.class, "test.war")
                .addClasses(Member.class, MemberService.class, MemberRepository.class,
                        MemberRegistration.class, Resources.class)
                .addAsResource("META-INF/persistence.xml", "META-INF/persistence.xml")
                .addAsWebInfResource("arquillian-ds.xml")
                .addAsWebInfResource(EmptyAsset.INSTANCE, "beans.xml");
       }
    }

Ok, quick sin/cos ( _high five to anyone who understands this_ ).  I really like sublime text, especially the multi row select. Ok, back to the show.


Next, we need to package our application, including all the web resources, for deployment and testing in the container.  I changed the file a little bit, since packaging all the "web" stuff takes a little bit of effort.  I add a helper method to create my deployments:

    public static WebArchive createDeployment() {
          return createTestDeployment();
      }


      public static WebArchive createTestDeployment() {
          WebArchive war = ShrinkWrap.create(WebArchive.class)
                  .addClasses(Member.class, MemberService.class, MemberRepository.class,
                          MemberRegistration.class, Resources.class, JaxRsActivator.class)
                  .merge(
                  	ShrinkWrap.create(GenericArchive.class)
                  	.as(ExplodedImporter.class)
                  	.importDirectory("src/main/webapp")
                  	.as(GenericArchive.class),"/")
                  .addAsResource("META-INF/persistence.xml", "META-INF/persistence.xml")
                  .addAsWebInfResource("arquillian-ds.xml")
                  .addAsWebInfResource(EmptyAsset.INSTANCE, "beans.xml");

          System.out.println(war.toString(true)); // this will show you what is packaged

          return war;
      }


OK,  time to add some dependencies to our pom.xml.  We are going to be using the Drone extensions from Arquillian so we need to add those.

First, make sure in the *dependecyManangment* section we have this as our first dependency:

    <dependency>
        <groupId>org.jboss.bom</groupId>
        <artifactId>jboss-javaee-6.0-with-tools</artifactId>
        <version>${jboss.bom.version}</version>
        <type>pom</type>
        <scope>import</scope>
    </dependency>

*Note: the version should be 1.0.2.Final and it should be specified in the properties*


Now, there should be some Arquillian dependencies already in this file,  find them and put this under them:

    <dependency>
        <groupId>org.jboss.arquillian.extension</groupId>
        <artifactId>arquillian-drone-webdriver-depchain</artifactId>
        <type>pom</type>
        <scope>test</scope>
    </dependency>


Ok,  now we can get back to code.  In our test lets add a resource to get us the URL context.

    @ArquillianResource
    URL contextUrl;

And also create a test that will open FireFox and display the homepage of our app

    @Test
    public void openFireFoxHomePage(@Drone FirefoxDriver driver) {
       driver.get(contextUrl.toString());
   }

For those following along, our new test class should look something like this:

    @RunWith(Arquillian.class)
    public class MemberRegistrationClientTest {

        @ArquillianResource
        URL contextUrl;


        @Deployment(testable = false)
        public static WebArchive createDeployment() {
            return createTestDeployment();
        }

        public static WebArchive createTestDeployment() {
            WebArchive war = ShrinkWrap.create(WebArchive.class)
                    .addClasses(Member.class, MemberService.class, MemberRepository.class,
                            MemberRegistration.class, Resources.class, JaxRsActivator.class)
                    .merge(ShrinkWrap.create(GenericArchive.class).as(ExplodedImporter.class).importDirectory("src/main/webapp").     as(GenericArchive.class),"/")
                    .addAsResource("META-INF/persistence.xml", "META-INF/persistence.xml")
                    .addAsWebInfResource("arquillian-ds.xml")
                    .addAsWebInfResource(EmptyAsset.INSTANCE, "beans.xml");

            return war;
        }

        @Test
         public void openFireFoxHomePage(@Drone FirefoxDriver driver) {
            driver.get(contextUrl.toString());
        }


    }

You should now be able to debug this test in an IDE, set a breakpoint and see the browser come up.

Here is the code up to this point: [Tag 0.0.1.drone](https://github.com/lholmquist/as-quickstarts/archive/0.0.1.drone.zip)

Here is my forked repo: [Drone Branch](https://github.com/lholmquist/as-quickstarts/tree/drone)


## Next Up

Now that we have a working test on Firefox,  lets try to automate it and submit something, then a Mobile Browser( since that was the point of this post).


[Beam Me Up Scotty... To the Next Post]({{ BASE_PATH }}{{ page.next.url }})

![lolcat](http://cdn.uproxx.com/wp-content/uploads/2012/04/lolcat-battlemech-alien2-uproxx3.jpg)
