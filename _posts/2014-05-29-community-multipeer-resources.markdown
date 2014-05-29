---
layout: post
title:  "Multipeer Connectivity Resources - Community"
date:   2014-05-29 18:00:00 +1000
categories:  ios multipeer
---
These are various tutorials/how-tos, etc, available on the web, produced by the iOS development community. I plan on updating this post as/when I find more resources. Please get in contact if you have any resources you'd like to contribute to this list.


## NSHipster on Multipeer Connectivity ##

You will find a great introduction to the whole Multipeer Connectivity framework on NSHipster. If you haven't already, [go read it](http://nshipster.com/multipeer-connectivity), then come back here. Future posts I write on this topic will largely assume that you are familiar with the contents of the NSHipster article.

As of writing, there are a couple of issues with the posted NSHipster article.

First of all, when advertising a service, make sure to keep a strong reference to the MCNearbyServiceAdvertiser (e.g. in a property), otherwise when the object goes out of scope it will be deallocated (and hence advertising will stop):

{% highlight objective-c %}
MCNearbyServiceAdvertiser *advertiser =
    [[MCNearbyServiceAdvertiser alloc] initWithPeer:localPeerID
                                      discoveryInfo:nil
                                        serviceType:XXServiceType];
advertiser.delegate = self;
[advertiser startAdvertisingPeer];
self.advertiser = advertiser; // important! keep a strong reference!
{% endhighlight %}

Make sure, of course, to nil out your reference to the advertiser when you no longer need it!

The second issue is an actual bug. When browsing for nearby services, you have two options:

1. Manually create a browser via the `MCNearbyServiceBrowser` class, and manage the UI yourself.
2. Use the built-in `MCBrowserViewController` to handle both UI and browsing.

The supplied example combines the two - first create a browser object, then pass it to the browser view controller. This is possible, but when doing so, _you should not manually invoke `[browser startBrowsingForPeers]`_ - the browser view controller does this automatically for you. Additionally, the browser view controller becomes the delegate for the browser.

So, the example code should be:

{% highlight objective-c %}
MCNearbyServiceBrowser *browser =
    [[MCNearbyServiceBrowser alloc] initWithPeer:localPeerID
                                     serviceType:XXServiceType];

MCBrowserViewController *browserViewController =
    [[MCBrowserViewController alloc] initWithBrowser:browser
                                             session:session];
browserViewController.delegate = self;
[self presentViewController:browserViewController animated:YES completion:nil];
{% endhighlight %}

Unless you need the browser object for another reason, though, it is simpler to just let the browser view controller create a browser itself:

{% highlight objective-c %}
// use the built-in browser view controller to connect to peers
MCBrowserViewController *browserViewController =
    [[MCBrowserViewController alloc] initWithServiceType:XXServiceType
                                                 session:session];
browserViewController.delegate = self;
[self presentViewController:browserViewController animated:YES completion:nil];
{% endhighlight %}

## Stack Overflow ##

This is a great place to get help, as well as help others. [See questions tagged `multipeer-connectivity`](http://stackoverflow.com/questions/tagged/multipeer-connectivity).



## Other Blogs ##

Besides NSHipster, I haven't found many tutorials online about the Multipeer framework. Here is a sampling of what I have found so far:

* _Understanding Multipeer Connectivity Framework in iOS 7_, Gabriel Theodoropoulos, - [Part 1](http://www.appcoda.com/intro-multipeer-connectivity-framework-ios-programming/) and [Part 2](http://www.appcoda.com/intro-ios-multipeer-connectivity-programming/)
* _Exploring the Multipeer Connectivity framework_, Gabriel Theodoropoulos, [Part 1: Project Setup](http://code.tutsplus.com/tutorials/exploring-the-multipeer-connectivity-framework-project-setup--mobile-23071) and [Part 2: Game Logic](http://code.tutsplus.com/tutorials/exploring-the-multipeer-connectivity-framework-game-logic--mobile-22660)

Please feel free to let me know of any notable exceptions to the list above!


## Demo projects on GitHub ##

Several people have developed demo projects showing how to approach writing multipeer code. A few examples are:

* [Easy Cards](https://github.com/azamsharp/EasyCards)
* [MCSessionP2P](https://github.com/shrtlist/MCSessionP2P) demonstrates auto browsing/connecting. It works well with only 2 peers, but with a larger number of peers it is extremely unreliable. Auto-connecting peers is a problematic area and non-trivial to implement and I hope to discuss it further in a later post.
