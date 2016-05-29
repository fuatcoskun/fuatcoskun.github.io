---
layout: post
title: How To Test Amazon Push Notifications
description: "How To Test Amazon Push Notifications"
modified: 2016-05-29
tags: [amazon, push, notification]
image:
  feature: abstract-11.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

One of the most powerful features of android platform is push notifications which you can keep your users informed about new features of your apps or show them some
special offers. This service is called as <a href="https://aws.amazon.com/sns/">"Amazon Device Messaging(ADM)"</a> on Amazon side.

I assume you already have integrated ADM into your application and trying to test push notifications on your Amazon device. Ok, let's see those push notifications. We have two ".sh" files which include
curl commands for getting access token and sending push notification to your registered device.

### Getting Access Token

Firstly you need an access token for sending push notification. Save the following codes as "adm_getAccessToken-test.sh".

{% highlight bash %}
#CLIENT_ID & CLIENT_SECRET parameters are application specific!!!
#You can get from Amazon Developer Console :)!!!
CLIENT_ID=amzn1.application-oa2-client.a...3
CLIENT_SECRET=f69c219247459c141...d1589c88b888eed7b
curl -X POST -H "Cache-Control: no-cache" -H "Postman-Token: e902b849-6578-9c43-92b4-6c55f2e246e9" -H "Content-Type: application/x-www-form-urlencoded" -d "grant_type=client_credentials&scope=messaging%3Apush&client_id=$CLIENT_ID&client_secret=$CLIENT_SECRET" https://api.amazon.com/auth/O2/token
{% endhighlight %}

Here we have two parameters as "CLIENT_ID" and "CLIENT_SECRET". You can learn your application specific
"CLIENT_SECRET" and "CLIENT_ID" from your <a href="https://developer.amazon.com/home.html">Amazon Developer Console</a> page.

After replacing these two parameters with your application and device information, you can run this "adm_getAccessToken-test.sh" or copy/paste it into your console. If everything goes well, you will obtain an
access token.

### Sending Push Notification

After getting access token, you are ready to send push notification. Save the following codes as "adm_sendPush-test.sh".

{% highlight bash %}
#ACCESS_TOKEN is obtained using adm_getAccessToken-test.sh
#REGISTER_ID is from adb logs.
ACCESS_TOKEN="Atc|MQEBIEshY-...76ouqKrndpvMw2YDHZ--oP6HIGgUgrLqGkI9wx0"
REGISTER_ID="amzn1.adm-registration.v2...M2b3c9PQ"
curl -X POST -H "Authorization: Bearer $ACCESS_TOKEN" -H "Content-Type: application/json" -H "X-Amzn-Type-Version: com.amazon.device.messaging.ADMMessage@1.0" -H "Accept: application/json" -H "X-Amzn-Accept-Type: com.amazon.device.messaging.ADMSendResult@1.0" -H "Cache-Control: no-cache" -H "Postman-Token: c7be00c4-232c-251e-3091-8e846214c3cd" -d '{
  "data":{"message":"Your message","title":"Amazon Push Message Title","tickerText":"Ticker ticker amazon"}
}' https://api.amazon.com/messaging/registrations/$REGISTER_ID/messages
{% endhighlight %}

You should obtain "REGISTER_ID" from adb logs after registering to ADM. Now you can run "adm_sendPush-test.sh" or copy/paste it into your console for sending push notification. If everything goes well, you will see a push notification on your amazon device.