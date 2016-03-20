---
layout: post
title: What is Singular Value Decomposition?
description: "What is Singular Value Decomposition"
modified: 2016-03-20
tags: [machine learning, feature extraction, svd]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---


Singular Value Decomposition can be seen as a feature extraction method which transforms correlated variables into a set of uncorrelated ones that better expose relationships on the original data. 
On the other hand, it is possible to determine dimension along which data points shows the most variation. It is possible find best approximation of the original data by reducing dimension.

There are 2 dimensional data points in the following figure which regression line running through them shows the best approximation of the original data. 
In this classification, inner class differences are small, and between class differences are big.  If we draw perpendicular line from each point to the regression line, and took the intersection of those lines as the approximation of the original data point, we would have a reduced representation of the original data that captures as much of the original variation as possible.

<figure>
	<img src="/images/figure1.png" alt="">
</figure>

