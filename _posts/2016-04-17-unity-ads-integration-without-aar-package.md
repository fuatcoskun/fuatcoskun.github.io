---
layout: post
title: Unity Ads Integration for Android without using aar package
description: "Unity Ads Integration for Android without using aar package"
modified: 2016-04-17
tags: [unity, ads, android]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

If you want to integrate Unity Ads into your native android project but also don't want to use android library for some reason, then you are in the right place to get some assist. I listed possible reasons of not using aar package in the following list.

- Not using Gradle build system.
- Using maven and facing issues while importing aar into your project.
- Want to add unity ads as library project.

First of all, you need to download <a href="https://github.com/Applifier/unity-ads-sdk/tree/master/unity-ads">unity-ads</a> android library project under <a href="https://github.com/Applifier/unity-ads-sdk/tree/master/">unity-ads-sdk</a>

Apply following steps to integrate unity ads library into your project.

<b>1.</b> After downloading unity-ads android library project, put unity-ads directory into libs folder in the root of your android project.

<b>2.</b> Cretae an empty "src" named folder in the root of your unity-ads directory.

<b>3.</b> Open local.properties file in unity-ads directory and,

- tell the source code should ve compiled with java 1.7
- Show your android sdk path in your local machine.

{% highlight bash %}
sdk.dir="C:\\..\\sdk"
renderscript.opt.level=O0
java.source=1.7
java.target=1.7
{% endhighlight %}

<b>4.</b> Open <b>your project's</b> AndroidManifest.xml file and add "xmlns:tools="http://schemas.android.com/tools" definition line under the "<manifest/>"

{% highlight xml %}
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
	  xmlns:tools="http://schemas.android.com/tools"
      package="..."
      android:versionCode="..."
      android:versionName="..."
      android:installLocation="auto">
{% endhighlight %}

<b>5.</b> Now you need to add unity ads activity into your applications manifest file, under the "<application>" node as follows :

{% highlight xml %}
<application
	....
	>

	....
	....
	<activity
	    android:name="com.unity3d.ads.android.view.UnityAdsFullscreenActivity"
	    android:configChanges="fontScale|keyboard|keyboardHidden|locale|mnc|mcc|navigation|orientation|screenLayout|screenSize|smallestScreenSize|uiMode|touchscreen"
	    android:hardwareAccelerated="true"
	    android:theme="@android:style/Theme.NoTitleBar.Fullscreen"
	    tools:ignore="UnusedAttribute" 
		/>

</application>
{% endhighlight %}

Now, you should be able to create an apk including unity ads resources and compiled codes using your build system.

If you want to learn how to implement unity ads on android, you can check <a href="https://github.com/Applifier/unity-ads/tree/master/android/example/src/com/unity3d/ads/android/example">this</a> example implementation.

I hope this post is helpful for everyone.
