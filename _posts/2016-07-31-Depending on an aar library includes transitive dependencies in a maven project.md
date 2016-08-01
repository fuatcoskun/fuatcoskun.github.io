---
layout: post
title: Depending on an aar library includes transitive dependencies in a maven project
description: "Depending on an aar library includes transitive dependencies in a maven project"
modified: 2016-07-31
tags: [gradle, maven, aar, jar, dependency, transitive]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

It can be difficult to handle android archive dependency issues if you are working on a maven based android project due to gradle and maven's default dependency type differences as "aar" amd "jar". Let's say there is an android archive(aar) library dependency which has a transitive "aar" dependency and you want to use this in your maven based android project. That's fine, you have added your dependency in your pom like this :

{% highlight maven %}

<dependency>
  <groupId>com.theartofdev.edmodo</groupId>
  <artifactId>android-image-cropper</artifactId>
  <version>2.2.5</version>
</dependency>

{% endhighlight %}

But your packaging process is going to be failed. Firstly, you need to define your dependency type as "aar" as below.

{% highlight maven %}

<dependency>
  <groupId>com.theartofdev.edmodo</groupId>
  <artifactId>android-image-cropper</artifactId>
  <version>2.2.5</version>
  <type>aar</type>
</dependency>

{% endhighlight %}

But it's not over, because above library has a transitive dependency called "appcompat-v7" which is also an "aar" library. Now we are in trouble, bacause we can not access to the pom file of
"android-image-cropper" library and change it's pom file. There is still something we can do in this situation. Simply exclude "appcompat-v7" dependency and include it manually by defining type as "aar.

{% highlight maven %}

<dependency>
  <groupId>com.theartofdev.edmodo</groupId>
  <artifactId>android-image-cropper</artifactId>
  <version>2.2.5</version>
  <type>aar</type>
    <exclusions>
      <exclusion>
        <groupId>com.android.support</groupId>
        <artifactId>appcompat-v7</artifactId>
      </exclusion>
    </exclusions>
</dependency>
<dependency>
  <groupId>com.android.support</groupId>
  <artifactId>appcompat-v7</artifactId>
  <version>23.2.1</version>
  <type>aar</type>
</dependency>

{% endhighlight %}

That's all :)