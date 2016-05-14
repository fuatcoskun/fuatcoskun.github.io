---
layout: post
title: How To Create Git Submodule with commit history
description: "How To Create Git Submodule with commit history"
modified: 2016-05-14
tags: [git, submodule, commit, history]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

Challenge of the last week for me was creating a git submodule without losing any information about commit history for one of our games. Creating a submodule is a straight job which has no difficulty. But if you change any file's directory by using "mv" or "git mv" command, all the commit history is lost. Now, I will explain how to create git submodule lossless.

My directory structure at the beginning was looking like this:

<figure>
    <img src="http://fuatcoskun.github.io/images/submodule1.png" alt="">
</figure>

Here Core, Drivers, Engine and Libs directories include some source code files which have been used by another game also. We have decided to sync those libraries under a submodule and I have created an empty github reopsitory called AndroidNativeLib for storing the submodule later on.

I will use Sources directory as target directory for creating a new git repository by preserving history, then I will remove unnecessary directories App, Breakpad and GoogleTest. Finally I will push remaining Core, Engine, Drivers and Libs directories with ".git" hidden folder which hold the commit history and other configuration.

### Creating New Repository By Preserving History

Let's start the journey :

I have used Git Bash for git operations on Windows. You can use terminal on Linux or Mac as you wish.

<b>1.</b> Root directory of the game is "Game" folder you can see on the screenshot above. Go to the root directory.
<b>2.</b> Ensure that you don't have any unstaged changes. I suggest you have a clean new repository without any modified files.
<b>3.</b> Run the following command for creating a new repository from Sources directory in the root on master branch.

{% highlight git %}
git filter-branch --prune-empty --subdirectory-filter Sources master
{% endhighlight %}

This was a filter-branch command which you can find more information about many other filter-branch command types <a href="https://git-scm.com/docs/git-filter-branch">here</a>.
After this, rewrite process will be started and all the commits will be rewrited for the source codes under Sources directory. You will see messages like below after rewriting process completed :

{% highlight git %}
Rewrite 5adef2baf54d91ce9ba572b9e5a370d8addd9dcd (1546/1546)
Ref 'refs/heads/master' was rewritten
{% endhighlight %}

You should see that your root directory only contains the files & folders under target directory. (Sources folder as below for me)

<figure>
    <img src="http://fuatcoskun.github.io/images/submodule2.png" alt="">
</figure>

<b>4.</b> Our new repository with history has been created but still points to our game repository. Now we need to remove remote repo definitions. Firstly, list existing remotes.</b>

{% highlight git %}
git remote -v
{% endhighlight %}

As result, name of the remotes defined are on the left side (here it is "origin") :

{% highlight git %}
origin  https://github.com/peakgames/...
origin  https://github.com/peakgames/...
{% endhighlight %}

<b>5.</b> Now remove remotes :

{% highlight git %}
git remote rm origin
{% endhighlight %}

<b>6.</b> Add new remote as pointing your new empty repository (AndroidNativeLib for me)

{% highlight git %}
git remote add origin https://github.com/peakgames/AndroidNativeLib.git
{% endhighlight %}

<b>7.</b> We can commit and push everything to our new repository.

{% highlight git %}
git push origin .
{% endhighlight %}

Now you can check your new repository on github and check history of source files.

<figure>
    <img src="http://fuatcoskun.github.io/images/submodule3.png" alt="">
</figure>

### Creating and Linking Submodule

Now we have a new repository ant we want to link this as a git submodule to our project. I will create a submodule called AndroidNativeLib just same as my library repository name.

<b>1.</b> Go to the directory you want to add submodule and run the following command. (For me it's Game/Sources)

{% highlight git %}
git submodule add https://github.com/peakgames/AndroidNativeLib
{% endhighlight %}

After that, a new file .gitmodules created in the root of your repository which includes submodule information :

{% highlight git %}
[submodule "Sources/AndroidNativeLib"]
    path = Sources/AndroidNativeLib
    url = https://github.com/peakgames/AndroidNativeLib
{% endhighlight %}

You can check  <a href="https://git-scm.com/book/en/v2/Git-Tools-Submodules">this document</a> for getting more information about submodules.

<b>2.</b> Commit & push everyting created under your main repository. Now you will be able to route to your submodule on github by clicking the link generated for you as below :

<figure>
    <img src="http://fuatcoskun.github.io/images/submodule4.png" alt="">
</figure>

That's all. Now we have a submodule including common libraries with existing commit history and it's is also a different git repository :)

