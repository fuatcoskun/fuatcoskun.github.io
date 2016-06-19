---
layout: post
title: OneSignal Custom Push Notifications
description: "OneSignal Custom Push Notifications"
modified: 2016-06-19
tags: [onesignal, custom, push, notification]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

If you have used custom notification in your android project, you already aware of RemoteViews and content field of android notification. In this blog post,
I have explained how to use custom notifications with OneSignal android SDK integration.

## Requirements

Approximately a month ago OneSignal has started to support custom push notifications. You need to use version <a href="https://github.com/OneSignal/OneSignal-Android-SDK/tree/2.4.0">2.4.0</a> or newer.
You can use <a href="https://github.com/OneSignal/OneSignal-Android-SDK/blob/2.4.0/OneSignalSDK.jar"OneSignalSDK.jar</a> directly in your project.

## Action

You have prepared a layout xml file in your res/layout folder. Now you need to extend NotificationExtenderService and implement "onNotificationProcessing" method.

{% highlight java %}
import android.support.v4.app.NotificationCompat;
import android.widget.RemoteViews;
import com.onesignal.NotificationExtenderService;
import com.onesignal.OSNotificationDisplayedResult;
import com.onesignal.OSNotificationPayload;

public class OneSignalNotificationExtender extends NotificationExtenderService {

    @Override
    protected boolean onNotificationProcessing(final OSNotificationPayload osNotificationPayload) {

        if(osNotificationPayload.additionalData != null && osNotificationPayload.additionalData.has(CUSTOM_LAYOUT)) {
            final OverrideSettings overrideSettings = new OverrideSettings();
            overrideSettings.extender = new NotificationCompat.Extender() {
                @Override
                public NotificationCompat.Builder extend(NotificationCompat.Builder builder) {
                    RemoteViews customNotification = new RemoteViews(getPackageName(),R.layout.custom_notification);
                    customNotification.setTextViewText(R.id.push_title, osNotificationPayload.title);
                    customNotification.setTextViewText(R.id.push_text, osNotificationPayload.message);
                    builder.setStyle(null);
                    return builder.setContent(customNotification);
                }
            };
            displayNotification(overrideSettings);
            return true;
        }
        return false;
    }
}
{% endhighlight %}

Most important part of this implementation is setting style of builder to "null". In default implementation of OneSignal, style of notification builder is set
as BigTextStyle. If your push notification is on most top of your notification bar, it's expanded version is shown and your custom layout is hidden. By setting style to null, you replace
the style and also say that you will use custom layout for your push notifications.

Returning true in onNotificationProcessing method means that you want to disable default one signal notification manager's behaviour and you also prevent duplicate push messages. I have used "customLayout" named
additional parameter for my push notifications sending from One Signal for differentiating normal and custom push notifications. If there is no "customLayout" parameter
in push notification, returning false will do the trick and one signal default notification manager will show the default push notification.

One last thing to do is defining your extender service in manifest file.

{% highlight xml %}
<application
...
...
>
<service
	android:name="net.peakgames.Game.OneSignalNotificationExtender"
	android:exported="false">
	<intent-filter>
		<action android:name="com.onesignal.NotificationExtender" />
	</intent-filter>
</service>
...
...
</application>
{% endhighlight %}

## Example layout 

You can use the following layout file by replacing your own images.

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
>

    <ImageView
            android:layout_width="fill_parent"
            android:layout_height="fill_parent"
            android:id="@+id/pushBack"
            android:layout_gravity="center_horizontal"
            android:src="@drawable/push_back"
            android:baselineAlignBottom="false" android:scaleType="fitXY"
    />

    <ImageView
            android:id="@+id/pushIcon"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_margin="8dp"
            android:contentDescription="@string/app_name"
            android:src="@drawable/icon_notification"
    />

    <ImageView
            android:id="@+id/pushTiles"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:contentDescription="@string/app_name"
            android:src="@drawable/push_tas"
            android:layout_centerVertical="true"
            android:layout_alignParentRight="true"
            android:layout_alignParentEnd="true"/>

    <LinearLayout
            android:orientation="vertical"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_alignParentTop="true"
            android:layout_toRightOf="@+id/pushIcon"
            android:layout_toEndOf="@id/pushIcon"
            android:layout_toLeftOf="@+id/pushTiles"
            android:layout_toStartOf="@+id/pushTiles"
            android:gravity="center_vertical">
        <TextView
                android:id="@+id/push_title"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text=""
                android:textColor="#000000"
                android:textSize="17sp"
        />
        <TextView
                android:id="@+id/push_text"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:text=""
                android:textColor="#414141"
                android:maxLines="2"
                android:minLines="1"
                android:textSize="14sp"

        />
    </LinearLayout>

</RelativeLayout>
{% endhighlight %}