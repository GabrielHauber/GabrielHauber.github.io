---
layout: post
title:  "Introduction to Multipeer Connectivity"
date:   2014-05-23 14:00:00 +1000
categories:  ios multipeer
---

I've been working on some functionality for my [SongSheet app][songsheet-appstore] that requires painless ad-hoc networking. As such I've chosen to utilise the new-with-iOS 7 Multipeer Connectivity framework.

I've done this for several reasons:

* I didn't want to have to mess with low-level networking code, sockets, etc
* Making connections should be as easy as possible for the user, hence transparent support of both ad-hoc Wi-Fi and Bluetooth
* Although the use-case is somewhat client-server-ish, the multipeer model still suits

What I didn't contend with was the pain such a seemingly small set of APIs would produce. A [search on StackOverflow][stackoverflow-multipeer] reveals a lot of others experiencing pain with the framework, too. Many have given up on it: the general consensus seems to be that it is "not ready for primetime" due to the many bugs, etc.

We had actually finished a companion app for SongSheet that we planned on releasing alongside the recently released version 2.0. It communicated with SongSheet via the Multipeer APIs. However, we had this issue whereby under certain circumstances losing the connection and then trying to automatically reconnect *would lock up and result in a reboot* of the device running the companion app.

Needless to say, we were not happy to release the app in that state and so we pulled the plug on it.

Now, however, I've gone back and revisited the whole framework, reworking everything from the ground up, and this time I think I've solved the issues. Yes, there *are* bugs in the framework to be worked around, but they seem rather predictable. I believe that the framework can *and should* be used in production apps, and I hope to have our companion app published with the next planned SongSheet release.


## What is a multipeer network? ##

At its simplest, a multipeer network is a collection of two or more *peers* connected to each other via a networking *session*. Facilitating the discovery and creation of sessions are *advertisers* and *browsers*. I will briefly discuss each of these concepts in turn. (I will provide a more thorough discussion with actual examples/source code in a later post.)

Note that the functions of browsing and advertising is only used for making connections. They are not needed once a connection has been made.


### Peer ###

A *peer* is a node in a multipeer session and should have a unique name in order to distinguish one peer from another. Each device running the same app connected to other devices in a network is a peer.

See the [`MCPeerID`](https://developer.apple.com/library/ios/documentation/MultipeerConnectivity/Reference/MCPeerID_class/Reference/Reference.html#//apple_ref/doc/uid/TP40013457) class reference in Apple's docs.


### Network session ###

The *session* consists of a set of peers connected together in a network. Each connected device has its own session containing the set of all other peers it is connected to. The session manages connections (either via the standard advertise/browse services provided as part of the API, or via other means of discovery) and can notify a delegate when messages are received from a peer, and when any connected peers change state.

Peers can send messages to one or more other peers via the session. Connections may also be encrypted or unencrypted depending on the requirements.

Normally, each peer within a session will have a reference to every other peer within the session. I have come across at least one scenario in the Multipeer Connectivity framework in which this is not the case - i.e. that peer A has a reference to peer B, when peer B *does not have* a reference to peer A. I will cover this scenario and how it arises in a later post.

See the [`MCSession`](https://developer.apple.com/library/ios/documentation/MultipeerConnectivity/Reference/MCSessionClassRef/Reference/Reference.html#//apple_ref/doc/uid/TP40013322) class reference in Apple's docs. Please note that currently a session is limited to a maximum of 8 peers (including "self").


### Advertiser ###

When a peer wishes to notify other devices that it wants other peers to connect to it, it *advertises* its service. The various classes that provide the advertising service wrap lower-level Bonjour APIs for convenience. It is possible, however, to use the lower-level APIs directly and build a custom advertising framework.

The advertiser receives incoming connection invitations and can either accept or reject them.

[`MCAdvertiserAssistant`](https://developer.apple.com/library/ios/documentation/MultipeerConnectivity/Reference/MCAdvertiserAssistant_class/Reference/Reference.html#//apple_ref/doc/uid/TP40013453) provides a ready-made class that "handles advertising, presents incoming invitations ot the user and handles users' responses".
[`MCNearbyServiceAdvertiser`](https://developer.apple.com/library/ios/documentation/MultipeerConnectivity/Reference/MCNearbyServiceAdvertiserClassRef/Reference/Reference.html#//apple_ref/doc/uid/TP40013320) provides programmatic control over the advertising/responding to invitation process and it is up to the app to provide (or not!) its own user interface to manage this.


### Browser ###

The *browser* is used to discover nearby peers that are advertising a service. Again, the browser classes are wrappers for lower-level Bonjour-related APIs, and you can still use the low-level APIs directly.

[`MCBrowserViewController`](https://developer.apple.com/library/ios/documentation/MultipeerConnectivity/Reference/MCBrowserViewController_class/Reference/Reference.html#//apple_ref/doc/uid/TP40013455) provides a standard UI that you can drop into your app for browsing available peers and initiating connection invitations.
[`MCNearbyServiceBrowser`](https://developer.apple.com/library/ios/documentation/MultipeerConnectivity/Reference/MCNearbyServiceBrowserClassRef/Reference/Reference.html#//apple_ref/doc/uid/TP40013323) provides programmatic control over the browsing and sending connection invitations process and it is up to the app to provide (or not!) its own user interface to manage this.


### Establishing a session ###

This is just a brief walk-through of the concept of establishing a multipeer network. I will provide actual source code in a later post.

Let's say you have 3 peers (`A`, `B`, and `C`) that wish to establish a network. To keep things simple, we assume that we assume that only `A` is advertising, and `B` and `C` are browsing (there is nothing stopping you from building an app where all peers both advertise and browse at the same time).

`B` and `C` discover `A` and notifies their users of `A`'s availability.

`B`'s user tells his device to connect to `A`. `A` receives the invitation, invites its user to accept or reject. Once `A`'s user indicates the connection should be accepted, `A` and `B` are now connected in a session.

This can be represented as:

> `A` --> `B`\\
> `B` --> `A`

Thus, `A` can send `B` messages, and vice versa.

Next, `C`'s user tells her device to connect to `A`. Again `A` receives the invitation, which is accepted. Now `C` is connected to `A` as well. However, and this is where a multipeer network is fundamentally different to that of a client/server relationship, *`B` is also now connected to `C`*.

Sow, now we have a 3-way network with 6 connections:

> `A` --> `B`\\
> `A` --> `C`\\
> `B` --> `A`\\
> `B` --> `C`\\
> `C` --> `A`\\
> `C` --> `B`

Even though `A` was the "advertiser", if it was now to disconnect from the session, `B` and `C` would remain connected to each other. Of course, if any new peers were to be connected to this network, either `B` or `C` (or both) would now have to advertise the service.



## Next steps ##

Well that's it for now. I hope that has provided you with a digestable introduction to the high-level concepts of Multipeer networking on iOS 7. In subsequent posts I will provide links to online resources, example code, common problems and solutions, etc. Please get in touch if you have any questions/queries or anything in particular you'd like me to focus on!


[songsheet-appstore]:http://bit.ly/TtSVoY
[stackoverflow-multipeer]:http://stackoverflow.com/questions/tagged/multipeer-connectivity
