---
layout: post
title: Extracting Java Method Signature for JNI calls
description: "How to extract java method signature for calling from c++ side"
modified: 2016-02-28
tags: [jni, java, c++]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Let's say you have a c++ project need to call java side using JNI interface. You know you need java method signature for this job and it's not so simple. 
Firstly you need to know java type signatures stated in the following table :

### Java Type Signatures
| Signature                 | Type                  |
| ------------------------- | --------------------- |
| Z                         | boolean               |
| B                         | byte                  |
| C                         | char                  |
| S                         | short                 |
| I                         | int                   |
| J                         | long                  |
| F                         | float                 |
| D                         | double                |
| L fully-qualified-class ; | fully-qualified-class |
| [ type                    | type[]                |

Now you have a reference to type signatures and some java methods ready(I assume) but don't know how to call it from dark side(c++). Here is some example java methods and their signatures below:

{% highlight java %}

Method :
public static void SetIntParam(String name, int value);
Signature :
(Ljava/lang/String;I)V
Method ID :
jmethodID jMethod = JNIEnvironment->GetStaticMethodID(jClass, "SetIntParam", "(Ljava/lang/String;I)V");

-----------------------------------------------------------------

Method :
public static void allowAds();
Signature :
()V
Method ID :
jmethodID jMethod = JNIEnvironment->GetStaticMethodID(jClass, "allowAds", "()V");

-----------------------------------------------------------------

Method :
public static void sendNotificationPhpCall(int chips, String friends);
Signature :
(ILjava/lang/String;)V
Method ID :
jmethodID jMethod = JNIEnvironment->GetStaticMethodID(jClass, "sendNotificationPhpCall",  "(ILjava/lang/String;)V");

{% endhighlight %}

At the end, if you don't want to handle these complex signature extraction process by yourself, I suggest you to use "**javap**" tool. You only need is compiled java class
including methods you want to extract signatures.

{% highlight bash %}
> cd CompiledJavaClassDirectory
> javap -p -s ClassName
{% endhighlight %}

It will show all the method's signatures like this :

{% highlight java %}
...
public static void SetStringParam(java.lang.String, byte[]);
   Signature: (Ljava/lang/String;[B)V
...
{% endhighlight %}

For more information about "javap" please follow: <https://docs.oracle.com/javase/7/docs/technotes/tools/windows/javap.html>




