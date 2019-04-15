---
layout: post
title:  "How to Mock Battery Level of Android Device"
date:   2019-04-12 12:30:30 -0500
categories: android,dumpsys,adb
---
# General Overview
A little while ago, I was tasked with some adjustments to our codebase that had a dependency on battery level - certain events would occur when the battery level dropped below 5%. There were also other events that would occur when battery was > 5%. After I made my adjustments, I wanted to make sure that things were working correctly. Well, in order to be sure of that, I had to see how the device behaves at different battery levels. So this is where the big question came in:

> How am I going to test this? Do I seriously have to wait until the battery drops below 5%?

My options initially consisted of impatiently waiting for the battery to drop or fumbling around with spare batteries in hopes to find one that I could quickly charge to just over 5%. However, with the wonders of the Internet, I quickly found an alternative solution via ADB and dumpsys. 

# Overview of `dumpsys`
We're all familiar with ADB and the capabilities that it provides. However, at least I haven't used `dumpsys` up until this point. In general, `dumpsys` is a tool that runs on Android devices that provides information about system services. It can be used as a diagnostic tool of an Android device by _dumping_ the current status of those services. If you run just plain Jane `adb shell dumpsys`, you're likely to see a massive amount of information that will be difficult to comprehend and filter through. That's because there are a **ton** of services running on the device and it will _dump_ all of them to `stdout`. Instead, you can use different `dumpsys` options to only output information for the service that you are interested in. To get a listing of all of the services that are running on the device that `dumpsys` can interact with, run the following:

`adb shell dumpsys -l`

Some of the services have their own corresponding help output. For example, if you run the following for the usb service:

`adb shell dumpsys usb -h`

...you'll see the help dialog for the usb service, which provides diagnostics for the current USB state.

# Mocking the Battery
In order to mock the battery level, we'll be (surprise!) using the battery service. Here are a couple of interesting commands:

`adb shell dumpsys battery` - This will output the current battery level state.

Let's take a look at the output of this last command:
```
Current Battery Service state:
  AC powered: false
  USB powered: true
  Wireless powered: false
  Max charging current: 0
  Max charging voltage: 0
  Charge counter: 0
  status: 2
  health: 2
  present: true
  level: 93
  scale: 100
  voltage: 4245
  temperature: 237
  technology: Li-ion
```
A couple things worth noting:

* The scale is the maximum value that _level_ can have
* To convert _temperature_ to Celsius, divide the value by 10

In addition, most of the values shown in this output can be adjusted by using the appropriate `dumpsys` commands:

`adb shell dumpsys battery set usb 0|1` - This will mock this device as if it were disconnected/connected to a USB charger

`adb shell dumpsys battery set ac 0|1` - This will mock this device as if it were disconnected/connected to an AC charger

`adb shell dumpsys battery set status 3` - Set the status of the battery to discharging. Note that the value for status is taken from the different STATUS constants of [BatteryManager](https://developer.android.com/reference/android/os/BatteryManager#BATTERY_STATUS_DISCHARGING). 

`adb shell dumpsys battery set health 2` - Set the health of the battery to good. Note that the value for health is taken from the different HEALTH constants of [BatteryManager](https://developer.android.com/reference/android/os/BatteryManager#BATTERY_HEALTH_GOOD).

`adb shell dumpsys battery set level 65` - Sets the battery level to 65%, assuming that _scale_ is 100. Otherwise, it will be set to 65/_scale_.

Once one of the _set_ commands is called, the device will stop interpreting the battery level from the hardware itself and will instead read the battery level from `dumpsys` commands. So, this may initially cause a rather scary realization. You might be getting all excited about manipulating the battery level of the device, feeling like you're in complete control when panic finally strikes:

> How do I.... get it working back to how it was before? Do I need to power cycle the device? Even worse, is it going to stay like this forever?

No need to fret because all of this can be resolved with a very simple `dumpsys` command:

`adb shell dumpsys battery reset`

Now the device is reverted back to reading the battery level from the hardware. 

Hope this all helps and happy coding everyone!

## References

https://www.intellectsoft.net/blog/android-shell-part-1-mocking-battery-status/

https://developer.android.com/studio/command-line/dumpsys

