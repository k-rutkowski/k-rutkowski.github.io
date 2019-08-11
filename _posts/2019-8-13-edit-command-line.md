---
layout: post
title: Editing shell command and shell vi mode
tags: [ linux ]
---

When typing a long command in a shell there is a high chance to make a mistake. And to correct that mistake you need to move the cursor back and forth the line which can be exhausting. There is a trick to make editing the command a little bit easier.

<!--more-->

At any moment in a shell, you can hit <strong>CTRL+X</strong>, <strong>CTRL+E</strong>. This will open your default editor with the current command line in it. Now you can make corrections, save, close editor and the corrected command will be executed.

There's more. If your preferred editor is <strong>vim</strong> you may want to consider switching your whole shell into <strong>vi mode</strong>. Just add this line to your shell config file (<i>.bashrc</i>, <i>.bash_profile</i>, <i>.zshrc</i>, etc.):
{% highlight bash %}
set -o vi
{% endhighlight %}

and now any time during typing you can press <strong>ESC</strong> to switch to normal mode just like in <strong>vi</strong>. This allows you to navigate and edit the line using <strong>vi</strong> key bindings. And if that's not enough you can hit <strong>v</strong> when in normal mode to open a full-screen editor.

The default editor can be set with the following command:
{% highlight bash %}
# update-alternatives --config editor
{% endhighlight %}
