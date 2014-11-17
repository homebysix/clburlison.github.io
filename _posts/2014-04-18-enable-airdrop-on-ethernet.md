---
filename: 2014-04-18-enable-airdrop-on-ethernet.md
layout: post
title: "Enable AirDrop on Ethernet"
date: 2014-04-18 08:43:36 -0500
modified: 2014-05-06
comments: true
published: true
keywords: AirDrop, Ethernet, cli, Terminal,
description: Quick post over enabling AirDrop over Ethernet.
categories: 
- osx
---
A quick post over enabling AirDrop to use an ethernet connection along with the default wireless connection. 

---

> AirDrop helps you share items with others nearby...a quick way to share files wirelessly between two Macs, without having to connect to an existing network.
>
> --Apple KB Article

AirDrop provides a great file-sharing experience and numerous departments at my workplace have grown to love it. The service is good for simple file exchanges without needing to use a files-share. That is until someone in the audio video department tries to send five (5) or more gigabytes of video files to someone sitting right next to him. This starts to cause problems as people experience slow downs and decide to wait instead of using our gigabit ethernet connection.

Apple provides the ability to use AirDrop over ethernet but did not enable it by default. We can only assume this was for good reason so enabling it could have unknown consequences. I have been using it for well over three (3) months without any issue, but your milage may vary. I will state that after issuing this change I needed to restart my machines for the devices to be able to utilize the AirDrop service (wireless or wired).

Type the following line of code into Terminal to make the change. Remember this needs to be made on both machines for it to take place.

**Enable AirDrop Over Any Network Connection**
{% highlight bash %}
defaults write com.apple.NetworkBrowser BrowseAllInterfaces 1
{% endhighlight %}

**Disable AirDrop Over Any Network Connection**
{% highlight bash %}
defaults write com.apple.NetworkBrowser BrowseAllInterfaces 0
{% endhighlight %}

---

Article: 
[Mac Basics: AirDrop](http://support.apple.com/kb/ht4783)