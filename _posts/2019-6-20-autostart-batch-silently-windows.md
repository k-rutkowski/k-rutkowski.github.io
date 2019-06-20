---
layout: post
title: Run batch script at Windows startup "silently"
tags: [ windows, batch ]
---

Normally launching a batch script opens a console window that stays open until the script is done. Usually, it's fine, but sometimes it would be better if the script performed in the background without popping a window. Especially if it's a task launched during system autostart and lasting until the user logs off.

<!--more-->

The solution I found is to launch our batch file not directly, but with a simple visual basic script "telling" the system to not show the window.

Below is a complete solution to the problem pointed by this post's title.

<ol>
<li>Alongside your batch file, lets call it <code>long_task.bat</code>, create <code>long_task_wrapper.vbs</code> script containing just this one line:
{% highlight bat %}
CreateObject("Wscript.Shell").Run "long_task.bat", 0, True
{% endhighlight %}
</li>
<li>Create a shortcut to the <code>.vbs</code> script</li>
<li>Hit <strong>Win+R</strong>, and type <code>shell:startup</code> to open autostart folder and move the shortcut there</li>
</ol>

That's all.
