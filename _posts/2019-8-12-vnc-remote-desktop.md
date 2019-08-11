---
layout: post
title: Setting up a VNC remote desktop
tags: [ linux, vnc ]
---

VNC (Virtual Network Computing) is a desktop sharing protocol available on almost every platform that has a desktop. It is very straightforward to set up but it requires a little effort to make it secure.

<!--more-->

What VNC does is sending keyboard and mouse input from client to server and transmitting a screen content back to the client. What it does not is making the transmission secure - this is the user's job.

One way to solve this issue, and probably the easiest one, is to set up an SSH tunnel between client and server and run the communication through it. In the example below, we use <strong>x11vnc</strong> as a server and <strong>TightVNC</strong> as a client program. We also assume the server is inaccessible directly from the internet, e.g. your home pc without a public IP address.

<br>
<h3>On the server side</h3>

First, we start the remote desktop:
{% highlight bash %}
$ x11vnc -geometry 1600x900 -ncache 10 -auth guess -localhost -display :0 -loop -passwdfile path/to/password/file
{% endhighlight %}

Important parameters:
* <strong>-geometry</strong> - resolution of the video sent to a client
* <strong>-localhost</strong> - prevents the connection from other machines in the network - it is fine in this set up because we'll be logged in to the localhost via ssh
* <strong>-loop</strong> - normally after the client disconnects the server is closed, this options tells the server to restart instead and wait for another client
* <strong>-passwdfile</strong> - points the file containing a password required to connect to the server

Then we create a reverse tunnel to <strong>far.server</strong> - the one through which we will access the actual VNC server:
{% highlight bash %}
$ autossh -M 0 -o "ServerAliveInterval 30" -o "ServerAliveCountMax 3" -o "ExitOnForwardFailure=yes" -fNR 2200:localhost:22 far_user@far.server
{% endhighlight %}

We use <strong>autossh</strong> instead of <strong>ssh</strong> to not worry about keeping the session alive and dealing with connection losses. <strong>autossh</strong> will handle it for us.

<br>
<h3>Connecting to the server</h3>

Creating the tunnel to VNC server through the <strong>far.server</strong> (so basically a tunnel inside a tunnel)
{% highlight bash %}
ssh -fNL 5900:localhost:5900 local_user@far.server -p 2200
{% endhighlight %}

Finally, we can start the VNC session:
{% highlight bash %}
$ vncviewer -encodings "copyrect tight hextile" -quality 2 -compresslevel 9 localhost
{% endhighlight %}
<br>
