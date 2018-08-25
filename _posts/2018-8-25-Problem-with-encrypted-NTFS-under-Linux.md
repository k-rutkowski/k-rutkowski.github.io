---
layout: post
title: Problem with encrypted NTFS partinion under Linux
tags: [ linux, veracrypt ]
---

Sharing NTFS partition between Linux and Windows can be very handy, but once in a while Linux may refuse to mount the partition with error message looking something like this:
{% highlight bash %}
$MFTMirr does not match $MFT (record 0).
Failed to mount '/dev/mapper/veracrypt2': Input/output error
{% endhighlight %}

There is a tool to fix this -- <em>ntfsfix</em>, but how to use it on the partition that is encrypted with Veracrypt?

Here is the solution:
<ol>
<li>Mount the volume with option <em>--filesystem=none</em> if using command line tool or by selecting <em>"Do not mount"</em> in the window prompting for password if using GUI.
</li>
<li>Execute following command as root (<em>veracrypt2</em> is for slot 2 - replace it with the slot you're using):
{% highlight bash %}
ntfsfix /dev/mapper/veracrypt2
{% endhighlight %}
Result should look like this:
{% highlight bash %}
Mounting volume... $MFTMirr does not match $MFT (record 0).
FAILED
Attempting to correct errors...
Processing $MFT and $MFTMirr...
Reading $MFT... OK
Reading $MFTMirr... OK
Comparing $MFTMirr to $MFT... FAILED
Correcting differences in $MFTMirr record 0...OK
Processing of $MFT and $MFTMirr completed successfully.
Setting required flags on partition... OK
Going to empty the journal ($LogFile)... OK
NTFS volume version is 3.1.
NTFS partition /dev/mapper/veracrypt2 was processed successfully.
{% endhighlight %}
</li>
<li>Unmount the volume with Veracrypt and mount again.
</li>
</ol>

From now it should work fine. At least until you use this partition under Windows again.

