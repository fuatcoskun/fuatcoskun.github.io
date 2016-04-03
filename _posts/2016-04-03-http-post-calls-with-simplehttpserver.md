---
layout: post
title: Http POST calls with SimpleHttpServer
description: "Http POST calls with SimpleHttpServer"
modified: 2016-04-03
tags: [python, simplehttpserver, http, post]
image:
  feature: abstract-5.jpg
  credit: dargadgetz
  creditlink: http://www.dargadgetz.com/ios-7-abstract-wallpaper-pack-for-iphone-5-and-ipod-touch-retina/
---

If you google "single line python server" you can find many references to SimpleHttpServer implementation of python. This is really useful for testing a remote http server
in your local machine by changing output of the service as you wish.

It's usage is really simple, so you can start the web server by typing the following command :

{% highlight bash %}
> python -m SimpleHTTPServer 8000
{% endhighlight %}

Now you have a web server on your local machine listening on port 8000. You can test it by using the url below in your browser :

{% highlight bash %}
> http://127.0.0.1:8000
{% endhighlight %}

The directory that you run the command is the root of your web server. You can put a simple json file into this directory and return it against your http "get" calls by adding this json file
into your url :

{% highlight bash %}
> http://127.0.0.1:8000/result.json
{% endhighlight %}

The restriction of this simple http server is that you can use only "get" method for your http calls. If you try to send post requests, you will get the following or any similar error on the console.

{% highlight bash %}
> code 501, message Unsupported method ('POST')
{% endhighlight %}

Let's add "post" method support to the SimpleHttpServer. The following python
script inherits SimpleHttpServer and adds do_POST method for simulating post http calls.

{% highlight python %}
#!/usr/bin/python

import SimpleHTTPServer
import SocketServer
import logging
import cgi
import sys

class PostServerHandler(SimpleHTTPServer.SimpleHTTPRequestHandler):

    def do_GET(self):
        logging.warning("GET CALL")
        SimpleHTTPServer.SimpleHTTPRequestHandler.do_GET(self)

    def do_POST(self):
        logging.warning("POST CALL")
        form = cgi.FieldStorage(
            fp=self.rfile,
            headers=self.headers,
            environ={'REQUEST_METHOD':'POST',
                     'CONTENT_TYPE':self.headers['Content-Type'],
                     })
        SimpleHTTPServer.SimpleHTTPRequestHandler.do_GET(self)

Handler = PostServerHandler
httpd = SocketServer.TCPServer(("", 8000), Handler)
httpd.serve_forever()

{% endhighlight %}

Save the above python code into a python file like "postServer.py" and start your server with the following command :

{% highlight bash %}
> python postServer.py
{% endhighlight %}

Now you can send http post requests to your local web server.

