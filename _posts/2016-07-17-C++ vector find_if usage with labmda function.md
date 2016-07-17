---
layout: post
title: C++ vector find_if usage with lambda functions
description: "C++ vector find_if usage with lambda functions"
modified: 2016-07-17
tags: [c++, vector, find_if, lambda]
image:
  feature: abstract-10.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

C++ language's standard vector has a function called "find_if" provides search functionality depends on a boolean return typed function. Let's say you have
an integer vector and trying to find an even number :

{% highlight c++ %}

#include <iostream>
#include <algorithm>
#include <vector>

bool IsEven (int i) {
	return ((i%2)==0);
}

int main () {
	std::vector<int> numberVector;

	numberVector.push_back(1);
	numberVector.push_back(3);
	numberVector.push_back(5);
	numberVector.push_back(6);
	numberVector.push_back(7);

	std::vector<int>::iterator it = std::find_if (numberVector.begin(), numberVector.end(), IsEven);
	printf("First even number : %d", *it);

	return 0;
}
{% endhighlight %}

But what if you want to pass an additional parameter to your search function and use lambda functions. Take a look to the following code snippet. We have a Popup object and a lambda function defines a search condition
depending on name of the popup.

{% highlight c++ %}

#include <iostream>
#include <algorithm>
#include <vector>
#include <functional>
#include "Popup.h"

int main () {

	std::vector<Popup*> popups;
	
	popups.push_back(new Popup("name1", "message1"));
	popups.push_back(new Popup("name2", "message2"));
	popups.push_back(new Popup("name3", "message3"));
	
	const char* popupName = "name2";

	std::vector<Popup*>::iterator it = std::find_if(popups.begin(), popups.end(), [popupName](Popup* popup)->bool {
		return strcmp(popup->getName(), popupName) == 0;
	});

	return 0;
}
{% endhighlight %}

It is so simple, lambda function's scope holds the local constant "popupName". Finally iterator "it" holds the result :)