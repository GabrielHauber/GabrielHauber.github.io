---
layout: post
title:  "How to make a game like candy crush tutorial: OS X port"
date:   2015-05-01 17:00:00 +1000
categories:  swift,spritekit,osx,crossplatform
---

Head on over to the [Ray Wenderlich](http://www.raywenderlich.com/) tutorial site to view my latest tutorial, [How to make a game like Candy Crush tutorial: OS X port](http://www.raywenderlich.com/87873/make-game-like-candy-crush-tutorial-os-x-port). This tutorial explains how to take an existing iOS-only Sprite Kit based game and turn it into a cross-platform iOS and OS X game.

While the Sprite Kit framework itself is the same across both iOS and OS X, differences arise when you see that on iOS it inherits from UIKit, and on OS X it is built on top of AppKit. This has implications especially for things like event handling (and, of course, app bootstrapping), so inevitably you need to write _some_ platform-specific code, but it is not that hard to minimise.

[Go read the tutorial](http://www.raywenderlich.com/87873/make-game-like-candy-crush-tutorial-os-x-port) for all the details!
