---
layout: post
title: Setting up a local GitHub Pages server
tags: [ linux, www, github ]
---

With GitHub Pages, you can easily set up a website and it's free. But when it comes to working on that website it's probably best to have a local server allowing you to see all the changes you've made before they're pushed to the world.

<!--more-->

I'm assuming the repository <code>username.github.io</code> is created and cloned to a local directory. Look <a href="https://guides.github.com/features/pages/">here</a> to see how to set up a new GitHub Pages project.

And now let's configure a local server able to host our website for development and testing purposes.

<br>
<h4>1. Install ruby and zlib dev packages</h4>
The first step is to install ruby 2.1.0 or newer with a dev package. You will also need a zlib dev package to compile some of the dependencies. In Ubuntu you can run the command:
{% highlight bash %}
$ apt install ruby ruby-dev zlib1g-dev
{% endhighlight %}

<br>
<h4>2. Install Bundler</h4>
{% highlight bash %}
$ gem install bundler
{% endhighlight %}

<br>
<h4>3. Create <strong>Gemfile</strong></h4>
Create a file named <strong>Gemfile</strong> in the root directory of your local repository (if it doesn't exist) and add these lines:
{% highlight bash %}
source 'https://rubygems.org'
gem 'github-pages', group: :jekyll_plugins
{% endhighlight %}

<br>
<h4>4. Install dependencies</h4>
{% highlight bash %}
$ bundle install
{% endhighlight %}

This has a chance to fail due to missing libraries in your system. If that's the case read the output carefully and check the mentioned log files to figure out which dependencies are missing.

<br>
<h4>5. Start the Jekyll server</h4>
{% highlight bash %}
$ bundle exec jekyll serve
{% endhighlight %}

Now you can preview your site at <code>http://localhost:4000</code>

If you want to host the site over the network specify an address and optionally a port:
{% highlight bash %}
$ bundle exec jekyll serve -H 192.168.1.101 -P 8080
{% endhighlight %}

<br>
The server automatically regenerates the site whenever you make any changes to it - just like on GitHub.

