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
	<img src="http://fuatcoskun.github.io/images/figure1.png" alt="">
</figure>

Second regression line which is perpendicular to first one gives poor result for approximating the original data. 
It captures as much of the variation as possible along the second dimension of the original data set.

<figure>
	<img src="http://fuatcoskun.github.io/images/figure2.png" alt="">
</figure>

It is possible to use these regression lines to generate a set of uncorrelated data points that will show subgroupings in the original data not necessarily visible at first glance. SVD reduces the high dimensional, highly variable set of data points to a lower dimensional space that exposes the substructure of the original data more clearly and orders it from most variation to the least.

From linear algebra point of view, we have “A” rectangular matrix which can be divided into product of three matrices are orthogonal “U”, diagonal “S”, transpose of orthogonal “V”.

<figure>
	<img src="http://fuatcoskun.github.io/images/eq1.png" alt="">
</figure>

In the above equation, U^T U=I  and  V^T V=I.  The columns of U are orthonormal eigenvectors of AA^T  ,  the columns of  V  are orthonormal eigenvectors of  A^T A  and  S is the diagonal  matrix containing the square roots of eigenvalues from U or V in descending order.
