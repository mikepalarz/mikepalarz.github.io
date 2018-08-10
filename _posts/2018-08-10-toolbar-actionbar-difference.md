---
layout: post
title:  "What's the Difference Between a Toolbar and ActionBar?"
date:   2018-08-10 16:10:30 -0500
categories: android
---

# What's the Difference?
I always mix these two up. I always forget about the difference, until the day comes where I need to start working on a new app and *something* makes me start investigating the app bar. It usually boils down to one of 2 scenarios:

1. I need to add the "up" button to the app bar
2. I'm starting to clean up my UI by applying some [Material Design](https://material.io/design/) to my app.

But now I'm probably confusing you even more because I've thrown in a third term into the mix: *app bar*. And I haven't even explained the other 2 yet. Let's break these down one at a time.

### What is the app bar?
The app bar is a general term for all of the items that appear under the status bar on the screen:

![App bar from a sample app](/assets/screenshots/appbar.png)

In general, it contains several important items, such as:

* Action items, such as a search button or heart icon to mark something as a favorite
* An overflow menu to include actions that are less likely to be used
* An optional "hamburger" icon for the navigation drawer

It's also a good place for providing your app's identity, such as including a logo and/or a background image for additional flair.

### What is the `Toolbar`?
The `Toolbar` is a widget that can be used to implement an app bar. It is currently the favored way to implement an app bar. There are other widgets that can also be used to provide an app bar, such as an `ActionBar`, which will be explained shortly. However, using a `Toolbar` ensures that the app bar will work on the widest range of devices and also allows for further customization as you further develop your app. A `Toolbar` can include any of the following optional elements:

* A navigation button, such as the "hamburger icon" for the navigation drawer, or an Up button.
* A branded logo image
* A title and (optionally) a subtitle
* One or more custom child views
* An action menu, which can include popular action items that are pinned to the end of the `Toolbar`. 

Additionally, a `Toolbar` can be arbitrarily placed anywhere within a nested view hierarchy. That means that you can theoretically place a `Toolbar` anywhere within your layout, although I haven't tried this out myself. Another awesome part about the `Toolbar` is that, as was mentioned previously, it supports a wide range of devices. That's because it's backwards compatible all the way back down to *API level 7*! That covers any device that can use the support library. Not only does it support these devices, but it also provides a [Material Design experience](https://material.io/design/components/app-bars-top.html) for all of them. Pretty sweet, right? For more info, read up on the `Toolbar` [here](https://developer.android.com/reference/android/support/v7/widget/Toolbar).

### What's the `ActionBar`?
The `ActionBar` is a widget that has been used to implement the app bar since API level 11. On the surface, it provides an app bar in the same way that the `Toolbar` does, with a few exceptions:

* Features were gradually added to the `ActionBar` class over various Android releases, meaning that the `ActionBar` would behave differently depending on what release of Android a device was running.
* It remains fixed at the top of the screen by the `Activity` that contains it by default.
* It doesn't provide a Material Design experience for all devices - only for devices that are running API level 21 or higher.

With all of this in mind, it's pretty easy to see why `Toolbar` is the preferred widget to use when adding an app bar. Ready to get started? Read the [developer docs on adding an app bar](https://developer.android.com/training/appbar/setting-up) for more info.

Best of luck and happy coding!
