---
layout: post
title: Jenkins Job Configuration For Git Submodule
description: "Jenkins Job Configuration For Git Submodule"
modified: 2016-05-22
tags: [git, submodule, jenkins, job]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

In previous week, I have published <a href="http://fuatcoskun.github.io/how-to-create-git-submodule-with-commit-history/">this blog post</a> about creating a git submodule as a new git repository by preserving commit history.

After this change in one of our games, it was necessary to update our compile and build jobs defined on jenkins service in our build machine. When you configure your jenkins job, you will see "Additional Behaviours" option in your build section as follows :

<figure>
    <img src="http://fuatcoskun.github.io/images/jws_1.PNG" alt="">
</figure>

Click on the Add button and select "Advanced sub-modules behaviours" option.

<figure>
    <img src="http://fuatcoskun.github.io/images/jws_2.PNG" alt="">
</figure>

Now the following section has been added into your build area and you should select "Recursively update submodules" checkbox.

<figure>
    <img src="http://fuatcoskun.github.io/images/jws_3.PNG" alt="">
</figure>

Ok, everything seems ok but when you build your job, you will see some error messages about git credentials as follows :

{% highlight git %}
Started by user Fuat Coskun
[EnvInject] - Loading node environment variables.
Building remotely on mobile-slave (mac-os) in workspace /Users/admin/Jenkins/workspace/Game-Compile-With-Submodule
...
FATAL: Command "git submodule update --init --recursive" returned status code 1:
stdout: 
stderr: Cloning into 'Sources/AndroidNativeLib'...
remote: Invalid username or password.
fatal: Authentication failed for 'https://github.com/peakgames/AndroidNativeLib/'
Clone of 'https://github.com/peakgames/AndroidNativeLib' into submodule path 'Sources/AndroidNativeLib' failed

hudson.plugins.git.GitException: Command "git submodule update --init --recursive" returned status code 1:
stdout: 
stderr: Cloning into 'Sources/AndroidNativeLib'...
remote: Invalid username or password.
fatal: Authentication failed for 'https://github.com/peakgames/AndroidNativeLib/'
Clone of 'https://github.com/peakgames/AndroidNativeLib' into submodule path 'Sources/AndroidNativeLib' failed

	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.launchCommandIn(CliGitAPIImpl.java:1591)
	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl.access$500(CliGitAPIImpl.java:86)
	at org.jenkinsci.plugins.gitclient.CliGitAPIImpl$6.execute(CliGitAPIImpl.java:893)
	at org.jenkinsci.plugins.gitclient.RemoteGitImpl$CommandInvocationHandler$1.call(RemoteGitImpl.java:152)
	at org.jenkinsci.plugins.gitclient.RemoteGitImpl$CommandInvocationHandler$1.call(RemoteGitImpl.java:145)
	at hudson.remoting.UserRequest.perform(UserRequest.java:118)
	at hudson.remoting.UserRequest.perform(UserRequest.java:48)
	at hudson.remoting.Request$2.run(Request.java:328)
	at hudson.remoting.InterceptingExecutorService$1.call(InterceptingExecutorService.java:72)
	at java.util.concurrent.FutureTask.run(FutureTask.java:266)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
	at hudson.remoting.Engine$1$1.run(Engine.java:63)
	at java.lang.Thread.run(Thread.java:745)
	at ......remote call to mobile-slave(Native Method)
	at hudson.remoting.Channel.attachCallSiteStackTrace(Channel.java:1361)
	at hudson.remoting.UserResponse.retrieve(UserRequest.java:221)
	at hudson.remoting.Channel.call(Channel.java:753)
	at org.jenkinsci.plugins.gitclient.RemoteGitImpl$CommandInvocationHandler.execute(RemoteGitImpl.java:145)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:606)
	at org.jenkinsci.plugins.gitclient.RemoteGitImpl$CommandInvocationHandler.invoke(RemoteGitImpl.java:131)
	at com.sun.proxy.$Proxy63.execute(Unknown Source)
	at hudson.plugins.git.extensions.impl.SubmoduleOption.onCheckoutCompleted(SubmoduleOption.java:83)
	at hudson.plugins.git.GitSCM.checkout(GitSCM.java:1047)
	at hudson.scm.SCM.checkout(SCM.java:485)
	at hudson.model.AbstractProject.checkout(AbstractProject.java:1276)
	at hudson.model.AbstractBuild$AbstractBuildExecution.defaultCheckout(AbstractBuild.java:610)
	at jenkins.scm.SCMCheckoutStrategy.checkout(SCMCheckoutStrategy.java:86)
	at hudson.model.AbstractBuild$AbstractBuildExecution.run(AbstractBuild.java:532)
	at hudson.model.Run.execute(Run.java:1744)
	at hudson.model.FreeStyleBuild.run(FreeStyleBuild.java:43)
	at hudson.model.ResourceController.execute(ResourceController.java:98)
	at hudson.model.Executor.run(Executor.java:374)
Finished: FAILURE
{% endhighlight %}

I know you expect that the same username and password will be used while cloning the submodule, but jenkins won't be able to do this due to <a href="https://issues.jenkins-ci.org/browse/JENKINS-20941">this bug</a> which is reported and still open.

The easiest workaround for this issue is that entering your own credentials manually during the first time cloning for your jenkins job. You can see the directory that jenkins created for your job in your error message :

{% highlight bash %}
/Users/admin/Jenkins/workspace/Game-Compile-With-Submodule
{% endhighlight %}

Go to this directory and run those commands for initializing your submodule for the first time :

{% highlight git %}
git submodule init
git submodule update
{% endhighlight %}

Now cloning process is over and you can run your jenkins job successfully. It won't ask your credentials again, but please remember that you should apply same steps again if you
create a new jenkins job with a git submodule.