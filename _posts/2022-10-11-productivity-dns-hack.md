---
layout: post
title: Productivity DNS hack
description: Breaking out of a habit by breaking DNS resolution
handle: productivity-dns-hack
date: '2022-10-11'
published: true
---

# Productivity DNS hack

Taking time off to enjoy internet content is good until it becomes a time-consuming habit.

A good way to break out of a habit is to add some friction. Something easy to become a little bit difficult can make a big difference, a pause before going through the motion of the default behaviour.

{% highlight sh %}
# /etc/hosts##
# Host Database
#
# localhost is used to configure the loopback interface
# when the system is booting. Do not change this entry.
##
127.0.0.1 localhost
255.255.255.255 broadcasthost
::1 localhost

# Artificial friction
127.0.0.1 www.twitch.tv
127.0.0.1 www.youtube.com

# Not quite ready to give up all my time sinks
# https://news.ycombinator.com/
{% endhighlight %}

Browsers have some local caches, so you might need to fiddle around a bit or restart for these to take in. Worst case, enjoy them one more time!

