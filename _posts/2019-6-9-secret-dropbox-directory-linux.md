---
layout: post
title: Secret the Dropbox directory on Linux OS
tags: [ linux, veracrypt, dropbox ]
---

This small tutorial shows how to put your Dropbox folder inside an encrypted container that can be mounted and unmounted on demand, using a keyboard shortcut. I believe this can be easily modified to work with other cloud services.

<!--more-->

<br>
<h4>1. Create a file container with veracrypt</h4>
{% highlight bash %}
$ veracrypt -t -c --volume-type=normal ~/.secret_container.vc --size=4G --encryption=aes --hash=sha-512 --filesystem=ext4 --pim=0 -k "" --random-source=/dev/urandom
{% endhighlight %}

You can play with the parameters, you can do all thing using creator instead of the command line, but make sure the filesystem is ext4 because Dropbox no longer works on other filesystems under the Linux.

<br>
<h4>2. Mount created volume in a directory of choice</h4>
{% highlight bash %}
$ mkdir -p ~/.secret_directory
$ veracrypt -t -k ""  ~/.secret_container.vc ~/.secret_directory
{% endhighlight %}

<br>
<h4>3. Set Dropbox folder location inside the encrypted directory</h4>
Run Dropbox and go to preferences. Set "Dropbox Folder location" to be inside <code>.secret_directory/</code>

<br>
<h4>4. Make sure Dropbox does not start with a system</h4>
Uncheck the related checkbox in Dropbox settings. Also, check the list of startup programs and remove Dropbox from them if it happened to be there.

<br>
<h4>5. Create mount and unmount scripts</h4>
<code>mount-dropbox.sh</code>
{% highlight bash %}
#!/bin/bash

container_path=~/.secret_container.vc
directory_path=~/.secret_directory

veracrypt $container_path $directory_path

dropbox_dir="$directory_path/Dropbox"
while [ ! -d "$dropbox_dir" ]; do
    sleep 5
done

dropbox start
{% endhighlight %}

<code>unmount-dropbox.sh</code>
{% highlight bash %}
#!/bin/bash

directory_path=~/.secret_directory

dropbox stop
veracrypt -d $directory_path
{% endhighlight %}

Place both scripts in your script directory (or anywhere you want) and <code>chmod +x</code> them.

<br>
<h4>6. Configure keyboard shortcuts firing the scripts</h4>
In Ubuntu you can add new global shortcuts in <em><strong>Settings -> Devices -> Keyboard</strong></em>.

<br>
And voila! You have a Dropbox folder that you can mount or unmount with a keystroke.

