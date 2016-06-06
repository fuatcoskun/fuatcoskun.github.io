---
layout: post
title: GoogleTest, g++ and 32-bit configuration
description: "GoogleTest, g++ and 32-bit configuration"
modified: 2016-06-05
tags: [g++, gcc, googleTest, 32-bit]
image:
  feature: abstract-3.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

I have worked on g++ compiler and google test for running unit tests of my huge c++ code base on MAC-OS-X operating system and integrating on Jenkins server as well. I have learned many things after some investigation and now I think this information can be useful for the people suffering due to same topic.

### Using .so files built by android ndk

First of all, my huge c++ code base is used in one of our top games as ndk built .so file and you know this is also a c++ library. It looks reasonable to use this prepared .so file for compiling with google test library and run unit tests, but as far as I see it is impossible. (If there is anyone who can achieve this, please write me!) Here is my stackoverflow topic about this issue :

## <a href="http://stackoverflow.com/questions/37571476/compiling-with-g-via-linking-so-file-built-by-android-ndk">http://stackoverflow.com/questions/37571476/compiling-with-g-via-linking-so-file-built-by-android-ndk</a>

### g++, gcc and GoogleTest configuration as 32-bit

After trying to use .so file, I have decided to compile my code base with g++ compiler with 32-bit option, because I have compiled my code base with 32-bit option with ndk so far and I don't want to waste my time with some casting, including etc. issues. I assume you already are familiar with GoogleTest and g++, cmake compile concepts. You need gtest-all and gtest-main libraries built on GoogleTest library. You can use the following command to prepare 32-bit google test (For more information about <a href="https://github.com/google/googletest/tree/master/googletest">google test</a>)

{% highlight bash %}
cmake <YOUR_GOOGLE_TEST_ROOT_DIRECTORY> -DCMAKE_BUILD_TYPE=release32
{% endhighlight %}

"CMAKE_BUILD_TYPE=release32" option forces the make process to use 32-bit option. After that you need to compile and obtain object files for gtest-all.cc and gtest_main.cc with 32-bit configuration :

{% highlight bash %}
g++ -isystem <YOUR_GOOGLE_TEST_ROOT_DIRECTORY>/include -I./ -pthread -c <YOUR_GOOGLE_TEST_ROOT_DIRECTORY>/src/gtest-all.cc -m32
g++ -isystem <YOUR_GOOGLE_TEST_ROOT_DIRECTORY>/include -I./ -pthread -c <YOUR_GOOGLE_TEST_ROOT_DIRECTORY>/src/gtest_main.cc -m32
{% endhighlight %}

"-m32" option here makes the magic for you and compiles with 32-bit option.

Now you can build archive files for using on compiling process of your unit tests.

{% highlight bash %}
ar rvs libgtest.a gtest-all.o
ar rvs libgtest_main.a gtest_main.o
{% endhighlight %}

Remaining part is just compiling my code base with the libraries libgtest.a and libgtest_main.a and creating an executable file which include all my unit tests. Next week I am gonna publish a blog post like "Google test tutorial" which will be include a simple code base and some unit tests.