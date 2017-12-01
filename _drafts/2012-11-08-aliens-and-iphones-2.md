---
layout: post
title: "Aliens and iPhones"
tagline: Huh?  part 2
description: "Show how to test mobile with Arquillian"
categories: [mobile, testing]
tags: [Arquillian, JBoss]
published: true
---



## Last Time

Previously on [Blog]({{ BASE_PATH }}{{ page.previous.url }}), we setup our environment and ran a successful test that launch the Firefox browser.

You might be saying, well thats great that i can open the homepage,  but what about automating clicks and such.  Hang on,  we are almost there.

## Refactor

Before we begin,  lets refactor our code a bit.  There is a little bit easier way to do what we did last time.

First, lets move the Drone Declaration to the top and make it more generic


    @RunWith(Arquillian.class)
    public class MemberRegistrationClientTest {
        .........
        @Drone
        WebDriver driver;
        .........


And also change the method signature of our test:

    @Test
    public void addMemberClientTest() {

       driver.get(contextUrl.toString());

    }


Finally,  we need to add a bit of config in the *arquillian.xml* file:


        <extension qualifier="webdriver">
            <property name="browserCapabilities">firefox</property>
        </extension>


I added it to the bottom.  This will tell our test what "WebDriver" to use.  If nothing was specified it would use HTMLUnit, which is a headless browser.

If you want, go ahead and run the test again.  You should get the same result as last time.  A firefox browser instance should open then close.


With these changes out of the way, we can now start on automating the browser

## Registration

Since the main function of this app is to register users, thats what we are going to automate.  So lets think about what we actually do.  What are the fields that we type into

* Name

* Email

* Phone Number

Then we click a button

* Register

Then what happens

* Ajax request to register

* Add a message that register was successful


Lets start with entering data into the input fields.

Here we use the *findElement* method:

    driver.findElement(By.id("name")).sendKeys("Luke");
    driver.findElement(By.id("email")).sendKeys("luke@luke.com");
    driver.findElement(By.id("phoneNumber")).sendKeys("1234567890");


You'll notice that we are finding the element by ID.  This is the same ID on the form element in our html.  Here it is in case you don't believe me:

    <input type="text" name="name" id="name" placeholder="Your Name" required autofocus/>


We also you the *sendKeys* function to, you guessed it, automate keys strokes.


The next thing we do is click the submit button to submit our form.  This is accomplished in a similar fashion.  We use *submit* instead of *sendKeys*

    driver.findElement(By.id("register")).submit();


On Successful registration a message is printed to the screen.  This is the test we want to run to check for it:

    assertEquals("Member Registered", driver.findElement(By.id("formMsgs")).getText());


You might be saying, "This is an ajax request,  How do i now it return?  Shouldn't that test fail?"  If you happen to say that, your correct.  This test would fail.  Thats why we need to add one more thing.

Arquillain has another extension called Graphene.  This is a neat product that lets us test Ajax requests.

First lets add the dependency to our pom.xml,  I added it under the last Arquillian dependency

    <dependency>
        <groupId>org.jboss.arquillian.graphene</groupId>
        <artifactId>arquillian-graphene</artifactId>
        <type>pom</type>
        <version>2.0.0.Alpha2</version>
        <scope>test</scope>
    </dependency>


*Notice that we have a version here.  We are using version 2.x , the version in the jboss.bom v 1.0.2.Final is still 1.0, so we need to overwrite it*

With this added, lets add the last bit to our test.

    waitModel().withMessage("Waiting For Registration Confirm").until(element(By.cssSelector("span.success")).isVisible());

This should be added between the submit and test.  What this basically says is to wait until the element containing the success message is visible,  then continue.

There is actually a timeout by default,  i think it might be 5 seconds or so.


You should now be able to run this test successfully.

If you want to see the actual key strokes in action,  you can put a break point on one of the sendKey lines and step through.  It's pretty neat.


Here is the code up to this point: [Tag 0.0.2.drone](https://github.com/lholmquist/as-quickstarts/archive/0.0.2.drone.zip)

Here is my forked repo: [Drone Branch](https://github.com/lholmquist/as-quickstarts/tree/drone)


## Next Up

Testing on Mobile.... I Promise

[Beam Me Up Scotty... To the Next Post]({{ BASE_PATH }}{{ page.next.url }})


![lolcat](http://i.chzbgr.com/completestore/2009/8/6/128940682356485244.jpg)
