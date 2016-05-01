---
layout: post
title: Using Emoji in Android Push Notification
description: "Using Emoji in Android Push Notification"
modified: 2016-05-01
tags: [android, push, notification, emoji, strings.xml]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Nowadays, it is so popular to send push notifications to clients including some sweet emojis for drawing attention and increasing the push notification click
probability.

If you want to use emojis by holding your text in a string variable or constant, you can use directly in the following format.

{% highlight java %}
String textWithEmoji = "This is your text with smile, \ud83d\ude03";
{% endhighlight %}

That's ok, but where could we find these unicode form of emojis'?

<a href="http://www.charbase.com/block/emoticons">http://www.charbase.com/block/emoticons</a>

After clicking one of those emojis in the list, you can get and use "Java Escape" form directly.

Ok, this was really easy, but what if you want to get this string from strings.xml file. Bad news is that you can not use directly as "\ud83d\ude03". After some investigation I have
decided to use url encoded form of emojis in strings.xml and decode it before setting my notification's title ot message.

On the <a href="http://www.charbase.com/1f603-unicode-smiling-face-with-open-mouth">same page</a> you can see the url encoded form of emoji as follows :

{% highlight html %}
q=%F0%9F%98%83
{% endhighlight %}

Just remove the "q=" part and store in the strings.xml file as shown below:

{% highlight xml %}
<string name="notification_emoji_smile">%F0%9F%98%83</string>
{% endhighlight %}

After reading the value from strings xml, you need to decode it. You can also combine it with another string from a variable or strings.xml.

{% highlight java %}
String emojiString = context.getString(R.string.notification_emoji_smile);
String decodedEmoji = URLDecoder.decode(emojiString, "UTF-8");
String notificaitonMessage = "Hi guys, " + decodedEmoji;
{% endhighlight %}

Let's say you found some emojis from another web site like this :

<a href="https://www.piliapp.com/emoji/list/">https://www.piliapp.com/emoji/list/</a>

No problem, you can URL encode it and apply the same method above. You can use the following web site to URL encode emoji.

<a href="http://www.freeformatter.com/url-encoder.html#ad-output">http://www.freeformatter.com/url-encoder.html#ad-output</a>

That's all, I wish happy emoji usages for everyone :)