---
layout: post
title: SSH basics
tags: [ linux, network, security ]
---

This post collects some useful stuff one can do with <code>OpenSSH</code>.

Almost every Linux distribution contains <code>ssh</code> client, but to use the full potential of <code>SSH</code> protocol - the complete <code>OpenSSH</code> package needs to be installed.

<!--more-->

The most important programs in the package are:
<ul>
	<li><strong><code>ssh</code></strong> - for logging into a remote server end executing commands</li>
	<li><strong><code>scp</code></strong> - for copying files between hosts via <code>SSH</code> protocol</li>
	<li><strong><code>sftp</code></strong> - also for copying files, but in an interactive fashion, similarly to the <code>ftp</code> client</li>
	<li><strong><code>sshd</code></strong> - SSH server</li>
	<li><strong><code>ssh-keygen</code></strong> - authentication key generator and manager</li>
</ul>

There are two config files: <code>sshd_config</code> and <code>ssh_config</code>. In &#42;nix systems they're usually located in <code>/etc/ssh/</code>. The first one is a server configuration and the second is client configuration. They allow to set up such parameters as default port, encryption algorithm, authentication methods, version of <code>SSH</code> protocol, location of a private key, key length, and more. Both configuration files have their man pages:
{% highlight bash %}
$ man sshd_config
$ man ssh_config
{% endhighlight %}

<h3>Basic stuff to do with <code>ssh</code></h3>

The simplest command to log into a remote terminal:
{% highlight bash %}
$ ssh example.com
{% endhighlight %}

This will try to log in as <i>user</i> where <i>user</i> is the current user logged on the local machine, so it's not very useful in most cases.

To log in as <i>john</i> use one of the following commands:
{% highlight bash %}
$ ssh john@example.com
$ ssh -l john example.com
{% endhighlight %}

The above commands assume the server works on the default port, which is <strong>22</strong> or whatever was set in <code>ssh_config</code>. This setting can be overridden like this:
{% highlight bash %}
$ ssh -p 2200 john@example.com
{% endhighlight %}

To be able to run programs with GUI on a remote machine, the <code>X Window System</code> protocol needs to be forwarded.
{% highlight bash %}
$ ssh -X john@example.com
{% endhighlight %}

Of course, the server has to be configured to allow forwarding the <code>X Window System</code> session.
<br/>

The program <code>ssh</code> can also be used to invoke a single command on the server:
{% highlight bash %}
$ ssh john@example.com 'ps -au |grep root'
{% endhighlight %}

This will print the list of processes run by <code>root</code> on <code>example.com</code> and then will immediately close the <code>SSH</code> session.


<h3>Copying files using <code>scp</code></h3>

Copying single file from local machine to the <i>home</i> directory of user <i>john</i> on the server:
{% highlight bash %}
$ scp sth.txt john@example.com:~/
{% endhighlight %}

This command copies file <code>sth.txt</code> from client's computer to <i>john</i>'s home directory on <code>example.com</code>

Few more examples with non default port and different directories:
{% highlight bash %}
$ scp -p 2222 sth.txt john@example.com:/path/on/sever/
$ scp -p 2222 sth.txt john@example.com:/path/on/server/new_file_name.txt
{% endhighlight %}

You can copy whole directories with switch <code>-r</code>:
{% highlight bash %}
$ scp -r some_directory john@example.com:/tmp/
{% endhighlight %}

Files can be copied from remote host to local machine:
{% highlight bash %}
$ scp john@example.com:~/tmp/remote_file.txt ./
{% endhighlight %}

And between two remote hosts:
{% highlight bash %}
$ scp john@example.com:~/sth.txt root@another_server:~/
{% endhighlight %}

A file can be copied to many hosts at once:
{% highlight bash %}
$ scp sth.txt john@example.com:~/ root@another_server:/tmp/
{% endhighlight %}

<h3>Port forwarding</h3>

<code>SSH</code> tunnels are created with switch <code>-L</code>:
{% highlight bash %}
$ ssh -L 8080:target_server:80 john@ssh_server
{% endhighlight %}

The above command creates a tunnel between port <code>8080</code> of the local computer and a port <code>80</code> of <code>target_server</code> that goes through <code>ssh_server</code>. From now you can access the <i>www</i> server (assuming that's what's working on port <code>80</code>) on target_server through <code>localhost:8080</code> and all packets go encrypted trough <code>ssh_server</code>. You don't even need access to <code>target_server</code> from your computer, it only has to be reachable from <code>ssh_server</code>.

If you don't need terminal access to <code>ssh_server</code> you probably should add switches <code>-f</code> and <code>-N</code>.
{% highlight bash %}
$ ssh -fN -L 8080:serwer1:80 john@serwer2
{% endhighlight %}

<ul>
<li><code>-f</code> makes <code>ssh</code> start in the background and ignore standard input (actually it forwards stdin from <i>/dev/null</i>).</li>
<li><code>-N</code> forbids executing commands on a remote host.</li>
</ul>

To make sure the connection was created you can use <code>netstat</code>:
{% highlight bash %}
$ netstat -a
{% endhighlight %}

It is also possible to make a tunnel that works backward. Imagine you want to have access to your pc at home from outside the home network and direct access is impossible because you don't have a public IP address. You can do it with the reverse SSH tunnel.
{% highlight bash %}
$ ssh -fN -R 2222:localhost:22 john@remote_server
{% endhighlight %}

This allows you to access your home pc via port <code>2222</code> on <code>remote_server</code>. So now you can log into <code>remote_server</code> and gain access to your home pc by executing:
{% highlight bash %}
$ ssh home_user@localhost -p 2222
{% endhighlight %}

Or log into your computer from any host:
{% highlight bash %}
$ ssh home_user@remote_server -p 2222
{% endhighlight %}

You only need to have a working SSH server on your home pc for it to work. Of course, you can forward that way any service, not only SSH sessions.

Some servers are configured to close the SSH session after a specific amount of time of idleness. To prevent that you can execute a never-ending command along with creating the tunnel, for example:
{% highlight bash %}
$ ssh -f -R 2222:localhost:22 john@remote_server 'while true; do sleep 30; echo > /dev/null; done'
{% endhighlight %}

Or you can use program <a href="http://www.harding.motd.ca/autossh/"><code>autossh</code></a> which has a session renewing mechanism.

<h3>Logging in without a password</h3>

If you log into a server frequently, typing a password every time may become tiring. In that case, it would be a good idea to generate a pair of RSA (or DSA) keys - private and public and use them instead of the password.

This can be achieved using program <code>ssh-keygen</code>:
{% highlight bash %}
$ ssh-keygen -t rsa
{% endhighlight %}

This will create two files:<code>id_rsa</code> and <code>id_rsa.pub</code>. The first one is the private key. It should be put in <code>~/.ssh</code> directory. The second one is the public key and it should be appended to the file <code>~/.ssh/authorized_keys2</code> on the server you want to log in without the password. After doing so you can log from your computer into the server via ssh or copy files between those hosts without providing a password (unless the server is configured differently).

The same way you can generate DSA keys:
{% highlight bash %}
$ ssh-keygen -t dsa
{% endhighlight %}

You can change the key length from the default 2048 bits:
{% highlight bash %}
$ ssh-keygen -b 4096 -t dsa
{% endhighlight %}

For security reasons, one private key should not be shared between hosts. The better way to allow logging in without a password from multiple computers is to generate different key pair on every computer and copy all public keys to the server's <code>.ssh/authorized_keys2</code> file.

For more information check the <code>man</code> pages of the mentioned tools and <a href="http://www.openssh.com/">the <strong>OpenSSH</strong> project website</a>.

