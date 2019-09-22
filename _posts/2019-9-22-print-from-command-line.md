---
layout: post
title: Printing documents only using a command line
tags: [ linux ]
---

Printing documents using the command line these days sounds completely obsolete, yet sometimes it may be pretty handy.
And the tool allowing it is available on almost every Linux distribution by default.

<!--more-->

In order to use the printer, we need to find out its name. To achieve that we can type the following command:
{% highlight bash %}
$ lpstat -p -d
{% endhighlight %}

This will print a list of available printers and their status.
<br/><br/>

Since we have the name of the printer we can send the print requests to it with program <strong>lp</strong>.
{% highlight bash %}
$ lp -d printer_name document.pdf                # the simplest usage
$ lp -d printer_name -o fit-to-page document.ps  # with small layout adjustment
{% endhighlight %}

Beside printable document files you can also print output of the shell commands:
{% highlight bash %}
$ ps axjf | lp -d printer_name -o fit-to-page    # print a process tree
$ man top | lp -d printer_name -P 1-2,4          # print first two and fourth page of the manual
{% endhighlight %}

To avoid typing the printer's name every time you can define the default printer via an environment variable. Add the following lines to your <i>.bashrc</i> or whatever shell config file you use.
{% highlight bash %}
PRINTER=printer_name
export PRINTER
{% endhighlight %}

Now you can skip the <i>-d</i> parameter in print command like that:
{% highlight bash %}
$ lp document.pdf
{% endhighlight %}
<br/>

Other usefull options:
<table style="font-size:90%">
<tr><td style="font-family: monospace"><strong>-o landscape</strong></td><td>print in landscape orientation</td></tr> 
<tr><td style="font-family: monospace"><strong>-o number-up=2</strong></td><td rowspan="5">print multiple document pages on one printed page</td></tr>
<tr><td style="font-family: monospace"><strong>-o number-up=4</strong></td></tr>
<tr><td style="font-family: monospace"><strong>-o number-up=6</strong></td></tr>
<tr><td style="font-family: monospace"><strong>-o number-up=9</strong></td></tr>
<tr><td style="font-family: monospace"><strong>-o number-up=16</strong></td></tr>
<tr><td style="font-family: monospace"><strong>-o cpi=<i>N</i></strong></td><td>set the number of characters per inch when printing plain text (default: 10)</td></tr>
<tr><td style="font-family: monospace"><strong>-o lpi=<i>N</i></strong></td><td>set the number of lines per inch when printing plain text (default: 6)</td></tr>
<tr><td style="font-family: monospace"><strong>-o page-left=<i>N</i></strong></td><td rowspan="4">set the page margin when printing plain text in points (1 point is 1/72th inch)</td></tr>
<tr><td style="font-family: monospace"><strong>-o page-top=<i>N</i></strong></td></tr>
<tr><td style="font-family: monospace"><strong>-o page-right=<i>N</i></strong></td></tr>
<tr><td style="font-family: monospace"><strong>-o page-bottom=<i>N</i></strong></td></tr>
</table>

<br/>
These are only the most basic things you can do with <strong>lp</strong>. For more options check the <strong>man<strong> pages.

