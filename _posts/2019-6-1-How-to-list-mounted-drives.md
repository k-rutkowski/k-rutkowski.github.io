---
layout: post
title: How to list mounted drives
tags: [ linux ]
---

Here are a few ways to show all drives mounted in your system. Useful when you, for example, don't know which device is mounted in a specific directory.

<!--more-->

The first method is to use <code>mount</code> command:
{% highlight bash %}
$ mount -l
{% endhighlight %}

and a result should look like this:
{% highlight bash %}
sysfs on /sys type sysfs (rw,nosuid,nodev,noexec,relatime)
proc on /proc type proc (rw,nosuid,nodev,noexec,relatime)
udev on /dev type devtmpfs (rw,nosuid,relatime,size=4032496k,nr_inodes=1008124,mode=755)
devpts on /dev/pts type devpts (rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000)
tmpfs on /run type tmpfs (rw,nosuid,noexec,relatime,size=817984k,mode=755)
/dev/sda5 on / type ext4 (rw,relatime,errors=remount-ro)
securityfs on /sys/kernel/security type securityfs (rw,nosuid,nodev,noexec,relatime)
tmpfs on /dev/shm type tmpfs (rw,nosuid,nodev)
/dev/sda4 on /mnt/windows type fuseblk (ro,relatime,user_id=0,group_id=0,default_permissions,allow_other,blksize=4096)
/dev/sda2 on /boot/efi type vfat (rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro)
/dev/sdb1 on /mnt/apps type fuseblk (rw,relatime,user_id=0,group_id=0,default_permissions,allow_other,blksize=4096) [Apps]
/var/lib/snapd/snaps/gnome-system-monitor_81.snap on /snap/gnome-system-monitor/81 type squashfs (ro,nodev,relatime,x-gdu.hide)
tmpfs on /run/user/1000 type tmpfs (rw,nosuid,nodev,relatime,size=817980k,mode=700,uid=1000,gid=1000)
{% endhighlight %}

Another one is to get information directly from <code>/proc</code> filesystem:
{% highlight bash %}
$ cat /proc/mounts
{% endhighlight %}

The output is very similar to what previous command prints:
{% highlight bash %}
sysfs /sys sysfs rw,nosuid,nodev,noexec,relatime 0 0
proc /proc proc rw,nosuid,nodev,noexec,relatime 0 0
udev /dev devtmpfs rw,nosuid,relatime,size=4032496k,nr_inodes=1008124,mode=755 0 0
devpts /dev/pts devpts rw,nosuid,noexec,relatime,gid=5,mode=620,ptmxmode=000 0 0
tmpfs /run tmpfs rw,nosuid,noexec,relatime,size=817984k,mode=755 0 0
/dev/sda5 / ext4 rw,relatime,errors=remount-ro 0 0
securityfs /sys/kernel/security securityfs rw,nosuid,nodev,noexec,relatime 0 0
tmpfs /dev/shm tmpfs rw,nosuid,nodev 0 0
/dev/sda4 /mnt/windows fuseblk ro,relatime,user_id=0,group_id=0,default_permissions,allow_other,blksize=4096 0 0
/dev/sda2 /boot/efi vfat rw,relatime,fmask=0077,dmask=0077,codepage=437,iocharset=iso8859-1,shortname=mixed,errors=remount-ro 0 0
/dev/sdb1 /mnt/apps fuseblk rw,relatime,user_id=0,group_id=0,default_permissions,allow_other,blksize=4096 0 0
tmpfs /run/snapd/ns tmpfs rw,nosuid,noexec,relatime,size=817984k,mode=755 0 0
nsfs /run/snapd/ns/libreoffice.mnt nsfs rw 0 0
nsfs /run/snapd/ns/vlc.mnt nsfs rw 0 0
{% endhighlight %}

And finally, if you prefer output formatted in a more readable fashion there is a tool called <code>df</code>&nbsp;-&nbsp;file system disk space usage inspector.
{% highlight bash %}
$ df -aTh
{% endhighlight %}

with output looking like this:
{% highlight bash %}
Filesystem             Type             Size  Used Avail Use% Mounted on
sysfs                  sysfs               0     0     0    - /sys
proc                   proc                0     0     0    - /proc
udev                   devtmpfs         3.9G     0  3.9G   0% /dev
devpts                 devpts              0     0     0    - /dev/pts
tmpfs                  tmpfs            799M  2.0M  797M   1% /run
/dev/sda5              ext4             165G   37G  120G  24% /
securityfs             securityfs          0     0     0    - /sys/kernel/security
tmpfs                  tmpfs            4.0G  142M  3.8G   4% /dev/shm
/dev/sda4              fuseblk          168G   92G   76G  55% /mnt/windows
/dev/sda2              vfat              96M   32M   65M  33% /boot/efi
/dev/sdb1              fuseblk          1.0T  114G  911G  12% /mnt/apps
nsfs                   nsfs                0     0     0    - /run/snapd/ns/libreoffice.mnt
nsfs                   nsfs                0     0     0    - /run/snapd/ns/vlc.mnt
{% endhighlight %}


