---
layout: post
title: C++ Builder Pattern - Method chaining
description: "C++ Builder Pattern in aspect of method chaining"
modified: 2016-03-06
tags: [c++, builder, pattern, chaining]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

If you are from java world like me, you most probably have chained methods using simple static builder class while instantiating your objects and you know this is builder pattern in aspect of chaining.
In one of our ndk-android game projects I decided to implement chaining rule of builder pattern instead of using classical "getter/setter" usage. Additionally I was
planning to use native heap instead of usign local stack memory. Additionally, I didn't want to use any friend classes which I don't find so natural.

I just wanted to instantiate Builder and build my actual object. But here, we have to pay attention to possible memory leaks and remove our builder instance on deconstructor of actual object. In the light
of these requirements I have implemented my "GiftModel" class as follows.

{% highlight c++ %}
#include "Core/Types.h"
#include <string>

class GiftModel {
public :
	uint32_t getCategoryId() {return categoryId;}
	uint32_t getGiftId() {return giftId;}
	uint32_t getPrice() const {return price;}
	void setPrice(uint32_t price) {this->price = price;}
	std::string getTitle() {return title;}
	std::string getImageName() {return imageName;}

	~GiftModel() {
		delete builder;
		builder = NULL;
	}

	class Builder {
		public :
			Builder() {
				giftModel = new GiftModel();
			}
			Builder* categoryId(uint32_t categoryId) {giftModel->categoryId = categoryId; return this;} 
			Builder* giftId(uint32_t giftId) {giftModel->giftId = giftId; return this;} 
			Builder* price(uint32_t price) {giftModel->price = price; return this;} 
			Builder* title(std::string title) {giftModel->title = title; return this;} 
			Builder* imageName(std::string imageName) {giftModel->imageName = imageName; return this;} 
			GiftModel* build() {giftModel->builder = this; return giftModel;}
		private :
			GiftModel* giftModel;
	};

private :
	uint32_t categoryId;
	uint32_t giftId;
	uint32_t price;
	std::string title;
	std::string imageName;
	Builder* builder;
};

{% endhighlight %}

You can see GiftModel holds the reference of Builder instance and removes it in deconstructor for preventing memory leak.


