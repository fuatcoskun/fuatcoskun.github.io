---
layout: post
title: Parse.Promise.when() array return type issue for multiple queries
description: "Parse backend when querying returns unexpected result"
modified: 2016-03-13
tags: [parse, parse.com, promise, javascript]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

If you are using Parse backend in one of your application/game like me, you already sadly know that Parse's backend services will be fully retired on January 28, 2017 and you probably started to migrate your data and cloud code to an
alternative db and backend service.

I have already started to migration process and decided to try MongoLab and AWS. Before to deploy our cloud code to AWS, I set up parse server environment on my local computer and running parse service succesfully. Problem is that when I call
Parse.Promise.when() with multiple commands like that :

{% highlight javascript %}
return Parse.Promise.when([query1, query2]).then(function () {
        console.log("arguments : " + JSON.stringify(arguments));
...
...
);
{% endhighlight %}

it returns different result than I am getting from parse servers. Difference is that returning json object on my local computer has only one key as "0" and an object array
as value.

{% highlight javascript %}
{
   "0":[
      {QuestionObject},
      {QuestionObject},
      ....
      {QuestionObject}
   ]
}
{% endhighlight %}

On the parse server instance, the same query gives the following result :

{% highlight javascript %}
{
   "0":{QuestionObject},
   "1":{QuestionObject},
   ...
   "16":{QuestionObject}
}
{% endhighlight %}

I firstly suspect that MongoDB configurations are different and that's why the data has been retrieved in different format and 
saw this doesn't make sense after 30 minutes of investigation. 

Finally we found that the root cause of this issue is a <a href="https://github.com/ParsePlatform/Parse-SDK-JS/commit/2880e5c4bc0f0bb8def825c3ccdf7b9c88691a9a">commit made on Parse Javascript SDK.</a>
Brief of this commit is seen as below:

{% highlight javascript %}
var arrayArgument = Array.isArray(promises);
var returnValue = arrayArgument ? [results] : results;
{% endhighlight %}

Here, "promises" is input queriy you pass into Parse.Promise.when() and you can see that if your input is an array, so consist of multiple queries, then square brackets are applied to returning object. This code change is commited
on December 10, 2015 and reverted on January 09, 2016 with this <a href="https://github.com/ParsePlatform/Parse-SDK-JS/commit/7da2856b18ca1672766612700139757300ecbf62">revert commit.</a>

For resolving this issue, I have downloaded the latest <a href="https://github.com/ParsePlatform/Parse-SDK-JS" >Parse JS SDK</a> and replaced ParsePromise.js with up to date version.




