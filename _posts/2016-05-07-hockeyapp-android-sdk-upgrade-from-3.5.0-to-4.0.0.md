---
layout: post
title: HockeyApp Android SDK Upgrade from 3.5.0 to 4.0.0
description: "HockeyApp Android SDK Upgrade from 3.5.0 to 4.0.0"
modified: 2016-05-08
tags: [android, hockeyapp, crash]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

<a href="https://www.hockeyapp.net/features/">HockeyApp</a> is a crash reporting/analysis tool for iOS, Android, OS X and Windows platforms supported by Microsoft for one and a half years approximately.

We use HockeyApp for tracking crash reports in several android games and were using 3.5.0 version. They announced 4.0.0 stable version a week ago which include reporting of impacted % crash free user data.
This is really a wonderful feature you can track success of your new releases which some other crash reporting tools like <a href="https://try.crashlytics.com/">Crashlytics</a> already have for years.

After this good news, it is time to upgrade our hockeyapp sdk version to 4.0.0. In 3.5.0 version, there was only a jar file you can easily import into your project. HockeyApp guys created an android archive (aar) for the version 4.0.0 which include some resources, localization texts used in Feedback, Authentication, built-in version update features. If you don't use these features and only want to get crash reports and user metrics, you don't have to use aar package. But where is the HockeySDK-4.0.0.jar? It is in the aar package.

Firstly download <a href="http://download.hockeyapp.net/sdk/android/HockeySDK-Android-4.0.0.zip">HockeySDK-Android-4.0.0.zip</a> file. After unpacking the zip file, you will see HockeySDK-4.0.0.aar file in libs directory. Change file extension to "zip" and unzip aar package :

<figure>
	<img src="http://fuatcoskun.github.io/images/hockeyapp1.png" alt="">
</figure>

### Using HockeyApp SDK 4.0.0 as jar

Get those "classes.jar" file and change the name to "hockey-sdk-4.0.0.jar". Replace your existing hockeyApp sdk jar with new one. If you are planning to use hockeyapp sdk as jar, you can follow "Code Changes" part.

### Converting HockeyApp 4.0.0 aar file to android library project

Ok, you don't want to use HockeyApp sdk as jar.

<b>1.</b> Cretae an empty "src" named folder in the root of your "HockeySDK-4.0.0" directory and put a dumy readme file into it.

<b>2.</b> Create a local.properties file in the root of your "HockeySDK-4.0.0" directory and define your android sdk path etc as follows :

{% highlight bash %}
sdk.dir=C:\\...\\sdk
renderscript.opt.level=O0
java.source=1.7
java.target=1.7
{% endhighlight %}

<b>3.</b> Create a project.properties file in the root of your "HockeySDK-4.0.0" directory : 

{% highlight bash %}
target=android-22
android.library=true
{% endhighlight %}

<b>4.</b> Move "classes.jar" into libs directory.

<b>5.</b> One last thing is to tell your android project that "HockeySDK-4.0.0" is a library project. Copy "HockeySDK-4.0.0" folder into libs folder of your android project and reference it in project.properties file of your android project.

{% highlight bash %}
android.library.reference.<library_index>=./libs/HockeySDK-4.0.0
{% endhighlight %}

Here "<library_index>" should be the index of this library for your application.

<b>6.</b> Finally <u>if you are using ant</u> for building your application, create build.xml file in the root of your "HockeySDK-4.0.0" directory :

{% highlight xml %}
<?xml version="1.0" encoding="UTF-8"?>
<project
    name="hockey-sdk"
    default="help" >

    <property file="local.properties" />
    <property name="java.target" value="1.7" />
    <property name="java.source" value="1.7" />
    <property file="ant.properties" />

    <property environment="env" />

    <condition
        property="sdk.dir"
        value="${env.ANDROID_HOME}" >

        <isset property="env.ANDROID_HOME" />
    </condition>

    <loadproperties srcFile="project.properties" />

    <fail
        message="sdk.dir is missing. Make sure to generate local.properties using &apos;android update project&apos; or to inject it through the ANDROID_HOME environment variable."
        unless="sdk.dir" />

    <import
        file="custom_rules.xml"
        optional="true" />

    <import file="${sdk.dir}/tools/ant/build.xml" />
</project>
{% endhighlight %}

### Code Changes

You will probabely see some breaking changes. Only thing I have faced was the following :

{% highlight bash %}
net.hockeyapp.android.Constants class doesn't have "TAG" field anymore.
{% endhighlight %}

After fixing breaking changes, you are ready to implement user metrics for tracking crash free users. You only need to register MetricsManager in onCreate callback of your activity as follows :

{% highlight java %}
MetricsManager.register(this, getApplication(), "YOUR_HOCKEYAPP_APPLICATION_ID");
{% endhighlight %}

If you have defined your hockeyapp application id in the manifest xml as shown below:

{% highlight xml %}

<application> 
  //your activity declarations and other stuff
  <meta-data android:name="net.hockeyapp.android.appIdentifier" android:value="@string/hockey_app_application_id" />
</application>  

{% endhighlight %}

then you can register MetricsManager without giving any application id.

{% highlight java %}
MetricsManager.register(this, getApplication());
{% endhighlight %}

From now on, user metrics will ve reported and after crossing a threshold defined by HockeyApp guys, you will see some new UI about crash free users in your HockeyApp dashboard :)