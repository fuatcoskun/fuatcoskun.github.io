---
layout: post
title: Custom Sounds for Android Push Notification
description: "Custom Sounds for Android Push Notification"
modified: 2016-06-26
tags: [android, custom, sound, push, notification]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Playing a custom sound for android push notifications is really simple with just one trick you have to know. If you are an impatient person who reads the last part of novels first, then move to the end of this post and get the trick :)

## Notification Configuration

{% highlight java %}

private Notification prepareNotification () {
    PendingIntent resultPendingIntent = PendingIntent.getActivity(context, 1, resultIntent, 0);
    Uri notificationSoundUri = prepareNotificationSoundUri(R.raw.yourSound);
    Notification.Builder builder =
                new Notification.Builder(context)
                        .setSmallIcon(R.drawable.icon)
                        .setContentTitle("Title")
                        .setContentText("Text")
                        .setContentIntent(resultPendingIntent)
                        .setDefaults(Notification.DEFAULT_ALL)
                        .setTicker("Ticker Text")
                        .setSound(notificationSoundUri);
    return builder.build();
}

private static Uri prepareNotificationSoundUri(int soundResourceId) {
    return Uri.parse("android.resource://" + context.getPackageName() + "/" + soundResourceId);
}

{% endhighlight %}

IF you support android sdk version lower than 16, then you should use NotificationCompat as follows : 

{% highlight java %}

private Notification prepareNotification () {
    PendingIntent resultPendingIntent = PendingIntent.getActivity(context, 1, resultIntent, 0);
    Uri notificationSoundUri = prepareNotificationSoundUri(R.raw.yourSound);
    NotificationCompat.Builder builder = new NotificationCompat.Builder(context)
                    .setSmallIcon(R.drawable.icon)
                    .setContentTitle("Title")
                    .setContentText("Text")
                    .setContentIntent(resultPendingIntent)
                    .setDefaults(Notification.DEFAULT_ALL)
                    .setTicker("Ticker Text")
                    .setSound(notificationSoundUri);
    return builder.build();
}

private static Uri prepareNotificationSoundUri(int soundResourceId) {
    return Uri.parse("android.resource://" + context.getPackageName() + "/" + soundResourceId);
}

{% endhighlight %}

That's all. You sent your push notification to your device but couldn't hear the sound you expect. Now, the mighty trick you should apply is ignoring default sound on notification builder object as follows :

{% highlight java %}
builder.setDefaults(~Notification.DEFAULT_SOUND);
{% endhighlight %}

Now you are ready to play your custom sound ;)