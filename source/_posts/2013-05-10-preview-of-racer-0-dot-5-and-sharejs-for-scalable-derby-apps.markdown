---
layout: post
title: "Preview of Racer 0.5 and ShareJS for scalable Derby apps"
date: 2013-05-10 10:52
comments: true
categories: 
---

Joseph and Nate have been sprinting on [Racer 0.5 and updates to ShareJS](2013/03/26/getting-derby-ready-for-prime-time/), which will soon be powering Derby applications. Racer has been completely rewritten to make it suitable for a production environment; it now scales horizontally across multiple servers and properly cleans up memory as new data is subscribed and unsubscribed. Based on our experience with the first version of Racer, we have also redesigned the code to be much more performant for complex application models.

We are still polishing off a few features, but want to show off an early preview of the upcoming hotness:

{% youtube uDzME15UxVM %}

The new version is still undergoing rapid development and needs lots more testing, but you can watch our progress and play around with it by checking out the [Racer 0.5 branch]() on GitHub. Derby has not yet been updated to work with the new Racer API, so you can't use Racer 0.5 with Derby just yet.
