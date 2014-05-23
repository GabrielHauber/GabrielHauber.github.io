---
layout: post
title:  "Multipeer Connectivity Resources - Apple"
date:   2014-05-23 17:00:00 +1000
categories:  ios multipeer
---

Before we get going in earnest with discussion about the details of multipeer networking on iOS, I thought it would be useful as a reference to list the various resources available to developers. This post concentrates on official Apple resources. The following post will provide links to various community resources available online.

Apple's documentation is generally quite excellent - good tutorials, excellent guides and comprehensive API docs. The Multipeer Connectivity framework, however, seems to be a little light-on in this regard. There are no guides providing typical usage scenarios and best-practice solutions. The API docs are just that - API docs. While useful if you've already got a pretty good idea how you are approaching things, they can be a little confusing when you are just getting started.

As such, the best place to start is either with third-party guides (hopefully my own will prove useful in this regard!) or with the video on Multipeer Connectivity from WWDC 2013.


## API documentation ##

[Available on the developer portal][api-docs] or, of course, in the documentation browser inside Xcode.


## Videos ##

[WWDC 2013: Nearby Networking with Multipeer Connectivity][wwdc-video]. Track: Core OS; Platform: iOS.

This video walks you through the rationale behind the framework, and presents a couple of examples - first using the built-in browser/advertiser view controllers, and secondly, how to advertise/browse and supply your own UI.

There is an important takeaway from the given example - the implications of which only hit me much later as I was trying to debug code that performed automatic discovery/connections between multiple (>3) devices.

The example has 3 devices with identifiers `Demijan`, `Jeff`, and `Gabe`.

`Jeff` and `Gabe` advertise the same service, while only `Demijan` browses for available services. `Demijan` sends an invite to both `Jeff` and `Gabe` to connect, and both accept the invitation. This might be obvious to a lot of people, but somehow I originally missed it (I haven't previously done a lot of work with inter-connected apps like this), but once `Jeff` and `Gabe` are connected to `Demijan`, *they are also automatically connected to each other*. I will further explore this implications of this (especially in regards to automated discovery/connections) in a later post.


## Sample code ##

[MultipeerGroupChat][groupchat-sample] demonstrates the core concepts pretty well.


## Apple developer forums ##

The [Core OS (General) forum][forum] is the right place to talk about the Multipeer Connectivity framework.



[wwdc-video]:https://developer.apple.com/videos/wwdc/2013/
[api-docs]:https://developer.apple.com/library/ios/navigation/#section=Frameworks&topic=MultipeerConnectivity
[groupchat-sample]:https://developer.apple.com/library/ios/samplecode/MultipeerGroupChat/Introduction/Intro.html#//apple_ref/doc/uid/DTS40013691
[forum]:https://devforums.apple.com/community/ios/core/coreosgen
