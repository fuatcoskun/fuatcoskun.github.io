---
layout: post
title: Unity Ads Integration for Android without using aar package
description: "Unity Ads Implementation Details for Native Android"
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

First of all, you need to initialize Unity ads as you can guess by giving context as initialization parameter. You also need to give a listener for getting callback and
some events about progress and completion.

{% highlight java %}

@Override
protected void onCreate(Bundle savedInstanceState) {
	super.onCreate(savedInstanceState);
	....
	initializeUnityAds();
}

private void initializeUnityAds() {

	UnityAds.init(this, Consts.UNITY_GAME_ID, new IUnityAdsListener() {
		@Override
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

Most important part of this listener implementation is "onFetchCompleted" callback which means some video ads are loaded, cached into memory and you can show if you want. Form now on, 

{% highlight java %}
UnityAds.canShow() 
{% endhighlight %}

method will return true. 
...
### Showing another video (Reloading video ad)
...