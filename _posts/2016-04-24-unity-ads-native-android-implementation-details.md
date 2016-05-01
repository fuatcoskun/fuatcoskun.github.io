---
layout: post
title: Unity Ads Simple Implementation for Native Android
description: "Unity Ads Simple Implementation for Native Android"
modified: 2016-04-24
tags: [unity, ads, android, java]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

My <a href="http://fuatcoskun.github.io/unity-ads-integration-without-aar-package/">previous blog post</a> was about integrating Unity Ads for a native android project using unity-ads android library project without aar package.

During the implementation phase, I faced some difficulties about reloading unity video ads and checking ad states. That's why I am writing this post about example implementation and some tricks.

### Example Implementation

First of all, you need to initialize Unity ads as you can guess by giving context as initialization parameter. Second parameters here is your game id which you can learn from <a href="https://unityads.unity3d.com/admin/#/games">unity ads games page.</a>

You also need to give a listener for getting callback and some events about progress and completion.

{% highlight java %}

@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	....
	initializeUnityAds();
}

private void initializeUnityAds() {

	UnityAds.init(this, "YOUR_GAME_ID", new IUnityAdsListener() {
		@Overrides
		public void onHide() {
			Log.d("UnityAds", "onHide");
		}

		@Override
		public void onShow() {
			Log.d("UnityAds", "onShow");
		}

		@Override
		public void onVideoStarted() {
			Log.d("UnityAds", "onVideoStarted");
		}

		@Override
		public void onVideoCompleted(String rewardItemKey, boolean skipped) {
			Log.d("UnityAds", "onVideoCompleted");
		}

		@Override
		public void onFetchCompleted() {
			Log.d("UnityAds", "onFetchCompleted");
			Log.d("UnityAds", "You can show video if you want!");
		}

		@Override
		public void onFetchFailed() {
			Log.d("UnityAds","onFetchFailed");
			Log.d("UnityAds", "You can't show video even if you want!");
		}
	});
	if(UnityAds.canShow()) {
		Log.d("UnityAds", "You can show video if you want!");
	}
}
{% endhighlight %}

Most important part of this listener implementation is "onFetchCompleted" callback which means some video ads are loaded, cached into memory and you can show if you want. From now on, 

{% highlight java %}
UnityAds.canShow() 
{% endhighlight %}

method will return true.

If you want to see demo video ads during development phase, you can enable test and debug mode :

{% highlight java %}
UnityAds.setDebugMode(true);
UnityAds.setTestMode(true);
{% endhighlight %}

After enabling debug mode, you can see Unity Ads logs but using "UnityAds" tag on logcat command.

{% highlight java %}
adb logcat -s UnityAds
{% endhighlight %}

Your listeners "onFetchCompleted" method is called and your video is ready to show. Use this to show the video:

{% highlight java %}
UnityAds.show();
{% endhighlight %}

After video has been completed or skipped, your listeners "onVideoCompleted" method is called. You can check skipped parameter for rewarding user or not.

### Showing another video (Reloading video ad)
This is the part that I have faced some difficulties. I was expecting that onFetchCompleted method will be called after first video is completed. I have tried following methods for triggering this in my ad showing flow :

- Called UnityAds.init(..) again after a video is completed.
- Called nothing and waited for onFetchCompleted will be called :)

Unfortunately onFetchCompleted is not called automatically after a video has been completed and also calling init second time is not working. In "onVideoCompleted" method, ads are not ready to show yet and if you call UnityAds.canShow() here it return "false".

You should use "onHide" callback of your listener for restarting your ad showing flow. 

{% highlight java %}
@Overrides
public void onHide() {
	if(UnityAds.canShow()) {
		Log.d("UnityAds", "Start your video show flow form beginning!");
	}
}
{% endhighlight %}

This "onHide" is called after user manually closes the ad. If you don't want to use this callback, you should check "UnityAds.canShow()" in any time for ensuring on availability of vidoe ads.