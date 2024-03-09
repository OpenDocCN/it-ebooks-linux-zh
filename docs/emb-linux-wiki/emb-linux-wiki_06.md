# Android 入口

[Android](http://en.wikipedia.org/wiki/Google_Android "wikipedia entry") 是 Google 和 开放手机联盟（Open Handset Alliance）开发的一款软件平台和操作系统，专为小型设备和智能手机设计。

包含 Android 开发相关的各个方面，从新手入门、Android Linux 内核、Android 系统、应用开发到 Android 社区以及各类基于 Android 开发的产品信息。

# 新手入门

Android 系统概览，从系统版本、历史、设计、架构到工具和各类教程。

# Introduction to Android

> From: [eLinux.org](http://eLinux.org/Android_Intro "http://eLinux.org/Android_Intro")

# Android Intro

## Contents

*   1 Overview
*   2 Videos
    *   2.1 Prototype Video
    *   2.2 G1 Product Video
*   3 Technical Information

## Overview

Android is a software platform and operating system written by Google and the Open Handset Alliance, designed for use in small form factor devices and smartphones.

The [Android wikipedia entry](http://en.wikipedia.org/wiki/Google_Android) is a really good place to get an overview of Android capabilities, history and direction. I won't duplicate the information from that site here.

The official web site for Android is [`www.android.com/`](http://www.android.com/). From here you can find links to:

*   [Android Market](http://www.android.com/market/) - This is the place where developers can post (for free or for sale) applications to run on Android-based devices, and where users can download these applications.
*   [Android Developer Site](http://developer.android.com/index.html) - Where software developers can learn how to write applications for Android devices, or modify aspects of the Android software itself.
*   [Android Open Source Project](http://source.android.com/) - This is where you can find the source code for the Android operating system (including Linux kernel, the libraries, Dalvik VM and rest of the Android system.)
*   [The Growth of Android in Embedded Systems](https://training.linuxfoundation.org/free-linux-training/download-training-materials/growth-of-android-in-embedded-systems)
    *   This Linux Training publication has some excellent material on the current (as of early 2013) status of Android as it relates to traditional embedded linux.

Clicking on the "What is Android" tab takes you to: [`www.android.com/about/`](http://www.android.com/about/), which has some high-level bullet points, and a video with a little bit of Android history.

## Videos

### Prototype Video

In November, 2007, Google released a video showing some of the features of their early prototype work on Android. See [`www.youtube.com/watch?v=1FJHYqE0RDg`](http://www.youtube.com/watch?v=1FJHYqE0RDg)

### G1 Product Video

The first phone product based on Android was the G1, manufactured by HTC and shipped by T-Mobile.

The G1 home page is at: [`www.t-mobileg1.com/`](http://www.t-mobileg1.com/). This site contains many videos and voiceovers describing G1 and Android features.

Engadget Hand's-on walkthrough of G1 features (September, 2008) [`www.engadget.com/2008/09/23/video-android-walkthrough-on-t-mobile-g1/`](http://www.engadget.com/2008/09/23/video-android-walkthrough-on-t-mobile-g1/)

## Technical Information

A good place to start, for technical information, is Google's ["What is Android?" page](http://developer.android.com/guide/basics/what-is-android.html%7C)

Another good place with technical information is [Android FAQ](http://android-dls.com/wiki/index.php?title=Android_FAQ)

Also, go to [Android Architecture](http://eLinux.org/Android_Architecture "Android Architecture") on this site.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Design and Architecture

> From: [eLinux.org](http://eLinux.org/Android_Architecture "http://eLinux.org/Android_Architecture")

# Android Architecture

See Google's [What is Android? page](http://developer.android.com/guide/basics/what-is-android.html) for an overview of Android components, and a diagram of the architecture.

The diagram on that page appears in every presentation I have ever seen about Android technical topics (with the exception of my own).

## Contents

*   1 Architecture Diagram
*   2 Overview presentations
*   3 Breakdown of running Android system
*   4 Relation to the Linux kernel
*   5 Java
    *   5.1 Java/Object Oriented Philosophy

## Architecture Diagram

Here is the Android Architecture Diagram, obtained from [here](http://developer.android.com/images/system-architecture.jpg).

![Android-system-architecture.jpg](http://eLinux.org/File:Android-system-architecture.jpg)

See also [Android internals diagram](http://www.makelinux.net/android/internals/)

Basically Android has the following layers:

*   applications (written in java, executing in Dalvik)
*   framework services and libraries (written mostly in java)
    *   applications and most framework code executes in a virtual machine
*   native libraries, daemons and services (written in C or C++)
*   the Linux kernel, which includes
    *   drivers for hardware, networking, file system access and inter-process-communication

## Overview presentations

*   [Android is not just Java on Linux](http://kobablog.wordpress.com/2011/05/22/android-is-not-just-java-on-linux/)
    *   Great presentation by Tetsuyuki Kobayashi overview of Android
*   See this Android Internals presentation by Karim Yaghmour
    *   [`www.opersys.com/blog/android-internals-101103`](http://www.opersys.com/blog/android-internals-101103)
    *   You'll find both the video and the slides there
*   [Mythbusters_Android.pdf](http://eLinux.org/images/2/2d/Mythbusters_Android.pdf "Mythbusters Android.pdf") Presentation by Matt Porter at ELC Europe
    *   Has bits and pieces showing problematic Android code and policies

## Breakdown of running Android system

A quick look at Android contents and programs running when Android starts is at:

*   [`benno.id.au/blog/2007/11/13/android-under-the-hood`](http://benno.id.au/blog/2007/11/13/android-under-the-hood)

## Relation to the Linux kernel

Here is [Greg Kroah-Hartmans presentation on Android](http://github.com/gregkh/android-presentation/downloads) from the CELF conference 2010, discussing how Google/Android work (or don't work) with the Linux community.

## Java

Java is used as a language for application programming, but it is converted into a non-java byte code for runtime interpretation by a custom interpreter (Dalvik).

### Java/Object Oriented Philosophy

Practicality is more important than purity in implementing the Android system.

Dianne Hackborn, one of the principal engineers working on Android, wrote:

It's not like I am a C programmer who doesn't like object oriented design. In fact prior to Android my primary language was C++... and honestly, Java really annoys me in the way it introduces so much more overhead to do things that I could express in very nice OO concepts in C++ with a much lighter-weight result.

Though Java has a lot of other nice attributes that make it good for Android, it also has its share of design flaws and misfeatures that mean we can't be totally beautifully OO as you would like.

Finally, going forward, our API conventions were defined in a way that allowed us to ship a well performing system on the hardware we had the time. As the situation changes (and it slowly is, but not enough yet) that could change... however, I will probably lean towards keeping those API conventions in place just for the sake of consistency with everything that currently exists. Of course if Android is successful and in 10 years from now we are designing a whole new next generation Android framework... well, then the world is a different place.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Necessary tools

> From: [eLinux.org](http://eLinux.org/Android_Tools "http://eLinux.org/Android_Tools")

# Android Tools

Here are some development tools useful for working with Android

## Contents

*   1 Android SDK
    *   1.1 host-side tools
        *   1.1.1 adb
            *   1.1.1.1 Running adbd on non-Android systems
        *   1.1.2 aapt
        *   1.1.3 ddms
        *   1.1.4 Fastboot
        *   1.1.5 Toolchains
        *   1.1.6 Emulator
        *   1.1.7 traceview
    *   1.2 target-side tools
        *   1.2.1 am
        *   1.2.2 dumpstate
        *   1.2.3 logcat
        *   1.2.4 monkey
        *   1.2.5 procrank
        *   1.2.6 service
        *   1.2.7 sqlite3
        *   1.2.8 toolbox
*   2 other tools
    *   2.1 agcc
    *   2.2 bootchart
    *   2.3 busybox
    *   2.4 smem
    *   2.5 strace
*   3 Eclipse
*   4 Hardware
    *   4.1 Serial Cable for G1

## Android SDK

### host-side tools

#### adb

adb is the android debugger - it also doubles as file transfer agent. The setup consists of an `adbd` on the target in the `/sbin` directory. On the host two programs are run: the `adb` application (in the SDK's `tools` directory) and an adb server, started by the adb application.

For emulators, adb will usually run automagically.

For real boards - with debugging over USB, you might need to do work, as is documented here: [`developer.android.com/guide/developing/device.html#setting-up`](http://developer.android.com/guide/developing/device.html#setting-up) .

For real boards that do not have a USB connection but have Ethernet instead, you might need to do a few tricks.

*   make sure that adbd runs on the board. If it doesn't run, you might want to check the `init.rc` file.
*   make sure that the network connection between host and the board is working - test pinging both ways.
*   on the host, type the following (you need to specify the board's IP address on the host):

```
 ADBHOST=<target-ip> tools/adb kill-server
  ADBHOST=<target-ip> tools/adb shell 
```

*   you should now get a prompt on the board, you can exit the prompt if you want.
*   `tools/adb devices` should now list the device.

##### Running adbd on non-Android systems

It is sometimes useful to use adbd on non-Android embedded Linux systems. Here is a patch that can be applied to adb (source as of 2014-04-05) to change it to avoid "Android-isms" in the build. Instructions are in a README.NONANDROID.TXT file.

[File:0001-Add-support-for-non-Android-use-of-adbd.patch](http://eLinux.org/File:0001-Add-support-for-non-Android-use-of-adbd.patch "File:0001-Add-support-for-non-Android-use-of-adbd.patch")

This patch can be applied to your adb source by cd'ing to the directory /system/core/adb, applying the patch with:

```
$ git am 0001-Add-support-for-non-Android-use-of-adbd.patch 
```

#### aapt

The Android Asset Packaging Tool is used to create, inspect and manage Android packages.

You can use this to see details about a package, it's resources, and xml information.

The [Android developer page on aapt](http://developer.android.com/guide/developing/tools/aapt.html) is somewhat meager.

See [Android aapt](http://eLinux.org/Android_aapt "Android aapt") for substantially more information.

#### ddms

The Dalvik Debug Monitor Server is a host-based tool which interacts with and Android target system and can show numerous bits of information, including the log, cpu and memory utilization, and lots of details about individual processes.

See the [DDMS developer guide](http://developer.android.com/guide/developing/tools/ddms.html)

#### Fastboot

[Android Fastboot](http://eLinux.org/Android_Fastboot "Android Fastboot") is a tool to boot and manipulate the partitions on an Android development phone.

#### Toolchains

Android provides pre-built toolchains (C/C++ compilers and linkers), but requires the installation of a java compiler (JDK) from an external source.

As of NDK version r5 (December 2010), the toolchains can now be used in standalone cross-compiler mode. See docs/STANDALONE-TOOLCHAIN.html in the NDK for information about this. Previously, the toolchains could be used within the build system, but it was difficult and error prone to compile native programs outside the Android build system with them.

#### Emulator

*   Emulator - See [`developer.android.com/guide/developing/tools/emulator.html`](http://developer.android.com/guide/developing/tools/emulator.html)

The emulator is a version of QEMU, which mimics the instruction set of an ARM processor, and the hardware that one might find on a mobile phone. The emulator runs on an x86 system, but executes an ARM linux kernel and programs. The flow of control is:

*   *   application ->
    *   dalvik VM ->
    *   C/C++ libraries ->
    *   ARM linux kernel ->
    *   emulated instructions and hardware (QEMU)->
    *   C libraries->
    *   x86 kernel ->
    *   real hardware

#### traceview

*   Google's main page describing traceview: [`developer.android.com/guide/developing/tools/traceview.html`](http://developer.android.com/guide/developing/tools/traceview.html)
*   [`www.bottomlesspit.org/file_download/2/Android_SDK_Traceview_tool.pdf`](http://www.bottomlesspit.org/file_download/2/Android_SDK_Traceview_tool.pdf)
    *   good overview presentation by Olivier Bilodeau
    *   presentation with speaker notes: [`www.bottomlesspit.org/file_download/3/Android_SDK_Traceview_tool_w_speakernotes.pdf`](http://www.bottomlesspit.org/file_download/3/Android_SDK_Traceview_tool_w_speakernotes.pdf)
*   [Performance Tuning Android Applications](http://newfoo.net/2009/04/18/performance-tuning-android-applications.html)
    *   straightforward article discussing traceview use to find an application bottleneck. April 2009.

### target-side tools

#### am

Activity Manager - can be used to start applications at the command line, or send intents to running applications.

#### dumpstate

dumps the state of the system. It scans the /proc filesystem, and collects various system properties, and puts them in a single report, suitable for sending to someone for support or development help.

#### logcat

This is the user tool for accessing the Android system log. This is implemented at a special option in adb (I'm not sure what the difference is between "adb logcat" and "adb shell logcat")

You can find lots of information about logcat on the [Android logger](http://eLinux.org/Android_logger "Android logger") page, and at [`developer.android.com/guide/developing/tools/adb.html#logcat`](http://developer.android.com/guide/developing/tools/adb.html#logcat)

#### monkey

#### procrank

procrank shows a listing of processes on the system, sorted by one of the memory utilization metrics. See [Android Memory Usage#procrank](http://eLinux.org/Android_Memory_Usage#procrank "Android Memory Usage")

#### service

Can be used to send an individual service message.

```
Usage: service [-h|-?]
       service list
       service check SERVICE
       service call SERVICE CODE [i32 INT | s16 STR] ...
Options:
   i32: Write the integer INT into the send parcel.
   s16: Write the UTF-16 string STR into the send parcel. 
```

On one forum, I saw that you could switch between portrait and landscape with:

```
$ service call window 18 i32 1 # to set to landscape on the emulator
$ service call window 18 i32 0 # to set to portrait on the emulator 
```

service list will show a list of different services that can be communicated with.

#### sqlite3

sqlite3 is a command-line database client program, for manipulating sqlite databases.

See [`www.higherpass.com/Android/Tutorials/Accessing-Data-With-Android-Cursors/`](http://www.higherpass.com/Android/Tutorials/Accessing-Data-With-Android-Cursors/) for a tutorial and some examples of using sqlite.

#### toolbox

toolbox is the equivalent of busybox on an Android system. That is, it is a multi-function program that provides many difference commands from a single binary. this includes things like: ps, ls, top, stop, start - commands to stop and start services on an Android system

See [Android toolbox](http://eLinux.org/Android_toolbox "Android toolbox") for details about individual commands.

## other tools

### agcc

*   [agcc](http://plausible.org/andy/agcc) - A wrapper tool for compiling native Android apps (linked directly to bionic)
    *   See [`android-tricks.blogspot.com/2009/02/hello-world-c-program-on-using-android.html`](http://android-tricks.blogspot.com/2009/02/hello-world-c-program-on-using-android.html)

### bootchart

*   See [Using Bootchart on Android](http://eLinux.org/Using_Bootchart_on_Android "Using Bootchart on Android")

### busybox

Android ships with a utility suite (called 'toolbox') that is not busybox.

You can get a binary busybox for Android [here](http://benno.id.au/blog/2007/11/14/android-busybox) The site includes instructions for easy installation on your device.

If you're interested in including busybox into a platform build:

*   precompiled binaries [here](https://github.com/Gnurou/busybox-android)
*   a [presentation](https://video.linux.com/videos/busybox-integration-on-android) on how to build (or not) and integrate busybox into platform build (slides available [here](https://events.linuxfoundation.org/images/stories/pdf/lf_abs12_sun.pdf)).

### smem

*   smem - smem is a tools for analyzing the memory usage on a system
    *   See [Using smem on Android](http://eLinux.org/Using_smem_on_Android "Using smem on Android") for more information

### strace

*   strace
    *   Statically linked binary available at: [`benno.id.au/blog/2007/11/18/android-runtime-strace`](http://benno.id.au/blog/2007/11/18/android-runtime-strace)
    *   Instructions for building Android strace - [`discuz-android.blogspot.com/2008/01/create-google-android-strace-tool.html`](http://discuz-android.blogspot.com/2008/01/create-google-android-strace-tool.html)

## Eclipse

The officially supported integrated development environment (IDE) is [Eclipse](http://www.eclipse.org/) (currently 3.4 or 3.5) using the Android Development Tools (ADT) Plugin, though developers may use any text editor to edit Java and XML files then use command line tools (Java Development Kit and Apache Ant are required) to create, build and debug Android applications as well as control attached Android devices (e.g., triggering a reboot, installing software package(s) remotely).

## Hardware

### Serial Cable for G1

You can build a serial cable to use with the G1, which is helpful to see kernel boot messages on the serial console.

See [`www.instructables.com/id/Android_G1_Serial_Cable`](http://www.instructables.com/id/Android_G1_Serial_Cable)

Back to [Android Portal](http://eLinux.org/Android_Portal "Android Portal")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Glossary

> From: [eLinux.org](http://eLinux.org/Android_Glossary "http://eLinux.org/Android_Glossary")

# Android Glossary

Here are some Android terms (some even with definitions!!)

See also the developer glossary at: [`developer.android.com/guide/appendix/glossary.html`](http://developer.android.com/guide/appendix/glossary.html)

Back to [Android Portal](http://eLinux.org/Android_Portal "Android Portal")

## Contents

*   1 A
*   2 B
*   3 C
*   4 D
*   5 E
*   6 F
*   7 G
*   8 H
*   9 I
*   10 J
*   11 K
*   12 L
*   13 M
*   14 N
*   15 O
*   16 R
*   17 S
*   18 T
*   19 V
*   20 W
*   21 Z

## A

[aapt](http://eLinux.org/Android_aapt "Android aapt") Android Asset Packaging Tool - a tool for creating, inspecting and modifying Android application packages.

Activity A single focused thing the user can do on an Android device. Also, a java class in the Android framework, which is used as the superclass for an Activity implementation. See [`developer.android.com/reference/android/app/Activity.html`](http://developer.android.com/reference/android/app/Activity.html). "An Activity presents a visual user interface for one focused endeavor the user can undertake." See Activity on the page: [`developer.android.com/guide/topics/fundamentals.html`](http://developer.android.com/guide/topics/fundamentals.html)

adb Android Debug Bridge - a tool for communicating between the host and a target Android system (including an emulator running on the host). See [`developer.android.com/guide/developing/tools/adb.html`](http://developer.android.com/guide/developing/tools/adb.html)

ADP1 Android Developer Phone 1

ANR Application Not Responding - this is a type of bug where Android believes a process has hung. The system may kill the process, and leave information about it in /data/anr for post-mortem analysis.

Android A robot resembling a human being - the name of the operating system produced by Google for mobile phones. Apparently, Andy Rubin, one of the original founders of Android, Inc. loves robots.

AndroidManifest.xml A file describing the contents, permissions and other attributes of an Android application package. See [`developer.android.com/guide/topics/manifest/manifest-intro.html`](http://developer.android.com/guide/topics/manifest/manifest-intro.html)

Android, Inc. A company founded by Andy Rubin and others to create a mobile phone operating system. Android, Inc. was acquired by Google in 2005.

ASE Android Scripting Environment - the old name of Scripting Layer for Android. See Android Scripting

## B

Binder An Interprocess Communication (IPC) mechanism. See [`cs736-android.pbworks.com/IPC-Binder`](http://cs736-android.pbworks.com/IPC-Binder) and [`groups.google.com/group/android-developers/msg/dc0e0e872de9b0d2`](http://groups.google.com/group/android-developers/msg/dc0e0e872de9b0d2)

Bionic small C library used in Android devices

Bootchart A mechanism to create visual charts of a Linux boot sequence, including the timing of process start and execution. See [Using Bootchart on Android](http://eLinux.org/Using_Bootchart_on_Android "Using Bootchart on Android")

## C

Cliq The US name for the Motorola Android phone.

Content Provider An piece of software on an Android system that provides information (content) to other software elements. See [`developer.android.com/guide/topics/providers/content-providers.html`](http://developer.android.com/guide/topics/providers/content-providers.html). Also, a class which is the superclass for code which acts as a content provider. See the [ContentProvider class documentation](http://developer.android.com/reference/android/content/ContentProvider.html)

Cupcake The code name for Android version 1.5.

## D

Dalvik Virtual Machine in which Android applications are run. This VM executes Dalvik bytecode, which is compiled from programs written in the Java language. Note that the Dalvik VM is not a Java VM (JVM).

Every Android application runs in its own process, with its own instance of the Dalvik virtual machine. Dalvik has been written so that a device can run multiple VMs efficiently. The Dalvik VM executes files in the Dalvik Executable (.dex) format which is optimized for minimal memory footprint. The VM is register-based, and runs classes compiled by a Java language compiler that have been transformed into the .dex format by the included "dx" tool.

See Android Dalvik VM for more information

Donut The code name for Android version 1.6

Dream Code name for the mobile phone hardware publicly called the t-Mobile G1, in the United States.

Droid The name for an upcoming Android phone by Motorola (I believe this is the high-end phone, and Cliq is the low-end phone?)

## E

Eclair The code name for Android version 2.1

## F

fastboot a program which communicates with the developer firmware, and which is capable of loading new software on the ADP1 phone (including re-writing the flash partitions on the device). See [Android Fastboot](http://eLinux.org/Android_Fastboot "Android Fastboot")

FreeType An open-source set of fonts and font system

Froyo *Frozen Yogurt* - The code name for Android version 2.2

## G

G1 The name of the first Android-based mobile phone, from t-Mobile.

Galaxy The name of the first Samsung Android phone

Gingerbread The code name for Android version 2.3

Goldfish The name of a virtual ARM platform provided by the emulator.

Goldfish executes ARM926T instructions and has hooks for input and output -- such as reading key presses from or displaying video output in the emulator. There is a "goldfish" configuration file for compiling the Linux kernel to run with this emulated platform.

Google A large web search company, and primary developer of Android

## H

Honeycomb The code name for Android version 3.0 - especially targeted at table computers

## I

Ice Cream Sandwhich Android version 2.4 or 3.1 - the successor to Gingerbread and/or Honeycomb (possibly indicating a development fork) See [`techcrunch.com/2011/01/11/android-ice-cream-sandwich/`](http://techcrunch.com/2011/01/11/android-ice-cream-sandwich/)

init the first user-space program run in the Android system. It is not a standard Linux-style 'init' program (which processes an /etc/inittab file). Rather, it processes a script called init.rc in the root directory of the file system. See [Android Booting#'init'](http://eLinux.org/Android_Booting#.27init.27 "Android Booting")

Intent A facility to send messages between different Android components. A message is conveyed using an Intent object, which is a data structure holding a description of an operation to be performed, or of something that has happened and is being announced.

## J

Java Java is a programming language originally developed by Sun, and used to develop Android applications.

It is important to note that while the Java language is used for Android applications, the Java bytecode and Java virtual machine are not. for more information, see the entry for Dalvik.

JDK Java Development Kit

Jellybean The code name for Android versions 4.1, 4.2 and 4.3

JNI Java Native Interface ([wikipedia entry](http://wikipedia.org/wiki/Java_Native_Interface)) is a programming framework that allows Java code to call or be called by "native" code (that is, code compiled in another language such as C, C++ or assembly).

## K

KitKat The release name for Android version 4.4

## L

Linux An open source operating system kernel, developed originally by Linus Torvalds, but over time by many thousands of developers worldwide.

Live-android A project to create an [Android live-CD](http://code.google.com/p/live-android/), for running Android on generic x86 platforms.

logcat A command to view messages in one of the system logs. See [Android logger](http://eLinux.org/Android_logger "Android logger")

## M

manifest file See AndroidManifest.xml

mahimahi The machine name used in the kernel for the development board used for the Nexus One product.

MSM Mobile Station Modem. Chipset manufactured by Qualcomm. Can for instance be found in cell phones containing the Snapdragon chipsets (HTC Desire/Nexus One).

## N

NDK [Native Development Kit](http://developer.android.com/sdk/ndk/index.html). A set of tools, build files and instructions to generate native code (usually libraries) to be used with Android systems. Native libraries are most often used as part of JNI (to allow Java code to call C code, or vice versa).

## O

OpenGL ES 3D graphics system and API for Android applications

## R

repo Android repository manager. This is a wrapper program (written in Python) over the git tool, for managing the multiple git repositories that make up the entire Android code base. See [`source.android.com/download/using-repo`](http://source.android.com/download/using-repo)

rild Radio-Interface-Link daemon. This is the daemon which handles communication between the rest of the Android system and the "radio interface" (otherwise known as the phone portion of an Android-based mobile phone system). In the simulator, since the phone hardware is not present, there is a program which runs to simulate the radio interface.

## S

Saphire

SL4A Scripting Layer for Android - an execution environment that let's users use scripting languages (such as Python or Ruby), instead of Java, to write programs for Android. See Android Scripting

SGL 2D graphics layer for Android applications

SQLite A powerful and lightweight relational database engine used by the Android system components, and available to all Android applications.

## T

TARGET_PRODUCT An environment variable used by the build system to indicate the product that the software should be built for. This and other TARGET_* variables are set using the choosecombo() function in build/envsetup.sh. If not set, the TARGET_* variables will use defaults when you run the 'm' alias, after source-ing build/envsetup.sh into your shell environment. Otherwise, use the choosecombo() function to set them.

ex: $ cd mydroid ; source build/envsetup.sh ; choosecombo

The options for TARGET_PRODUCT depend on entries in the AndroidProducts.mk files under build/target/products and vendor/*/*/AndroidProducts.mk in your repository.

toolbox The name of a multi-function program in the Android system. This program contains code for the single program toolbox to act like several different programs and utilities. Normally 'toolbox' is stored in /system/bin, and is symlinked to other names. It uses argv[0] to determine which program to behave like, when run. It is very similar in this regard to 'busybox', which another multi-function program used in many other embedded Linux systems.

Trout ARM linux kernel machine ID for the HTC Dream hardware (used in the t-Mobile G1 and the ADP1)

See [`www.arm.linux.org.uk/developer/machines/list.php?id=1440`](http://www.arm.linux.org.uk/developer/machines/list.php?id=1440)

## V

vold volume daemon - a process on an android system responsible for managing mounting and unmounting file system (volumes)

## W

wakelocks A kernel mechanism for Android power management. When a thread holds a wakelock, the kernel will refrain from entering a low-power state.

Back to [Android Portal](http://eLinux.org/Android_Portal "Android Portal")

## Z

zygote The first Dalvik virtual machine instance. All other java applications that are started in the system are spawned from zygote.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Tutorials and Courseware

> From: [eLinux.org](http://eLinux.org/Android_Tutorials "http://eLinux.org/Android_Tutorials")

# Android Tutorials

Here are some "getting started" tutorials for Android and Android development:

*   [`vis.berkeley.edu/courses/cs160-sp08/wiki/index.php/Getting_Started_with_Android`](http://vis.berkeley.edu/courses/cs160-sp08/wiki/index.php/Getting_Started_with_Android)
*   [Simple HOWTO building some linux apps and libraries from scratch on android](http://forum.xda-developers.com/showthread.php?t=631818)
    *   Contains instructions for building add-ons like busybox, jupp (text editor), libFLAC, xvid, nmap and dropbear
*   [From Unboxing Panda to Building and Loading an App](http://eLinux.org/Android_Tutorials_Unbox_to_App "Android Tutorials Unbox to App")
    *   Contains instructions for unboxing Panda, the hardware needed, where to get a pre-built Panda build, how to program it, how to install the Android tools and how to build and install the app on Panda. Also includes a link to the demo app, "DisableSuspend." This tutorial features builds from [`www.linaro.org`](http://eLinux.org/index.php?title=Linaro&action=edit&redlink=1 "Linaro (page does not exist)"), an industry consortium for improving ARM upstream support. It contains every step needed in one place.

## Contents

*   1 YouTube AndroidDevelopers channel
*   2 Karim Yaghmour presentations
    *   2.1 Karim's courseware
*   3 Michael Haim presentations
*   4 Free Electrons Android Courseware

## YouTube AndroidDevelopers channel

There are numerous videos (including tutorials) at the Android Developers channel on YouTube.

See [`www.youtube.com/profile?user=androiddevelopers#g/u`](http://www.youtube.com/profile?user=androiddevelopers#g/u)

## Karim Yaghmour presentations

*   [Android Internals](http://www.opersys.com/blog/abs-march2011) - Karim's presentations at [Android Builders Summit](http://events.linuxfoundation.org/events/android-builders-summit) 2011
*   [Porting Android to New Hardware](http://www.opersys.com/blog/abs-march2011) - Karim's presentations at [Android Builders Summit](http://events.linuxfoundation.org/events/android-builders-summit) 2011
*   [Android For Embedded Linux Developers](http://www.opersys.com/blog/andevcon-march2011) - Karim's presentation at [AnDevCon](http://www.andevcon.com/) 2011
*   [Understanding the Android System Server](http://www.opersys.com/blog/andevcon-march2011) - Karim's presentation at [AnDevCon](http://www.andevcon.com/) 2011

### Karim's courseware

Click on the "Courseware" thumbnail on the class page:

*   [`www.opersys.com/training/embedded-android`](http://www.opersys.com/training/embedded-android)
*   [`www.opersys.com/training/android-development`](http://www.opersys.com/training/android-development)
*   [`www.opersys.com/training/embedded-linux`](http://www.opersys.com/training/embedded-linux)
*   [`www.opersys.com/training/linux-device-drivers`](http://www.opersys.com/training/linux-device-drivers)

## Michael Haim presentations

Also, Michael Haim has produced a large number of useful presentations about Android topics. These are available at: [`www.abelski.com/`](http://www.abelski.com/)

You need to create an account to use these resources, but they are free for personal and academic use.

There are presenations available in the following categories:

*   Android Fundamentals
*   Android Workshops
*   App Widgets Development
*   Effective Programming
*   Android Testing
*   The Android Internals

## Free Electrons Android Courseware

Free Electrons has released their complete Android training materials, under the usual Creative Commons BY-SA 3.0 license: [`free-electrons.com/blog/free-android-training-materials/`](http://free-electrons.com/blog/free-android-training-materials/) . It contains more than 400 pages of slides and practical labs.

There's a public git tree and a LaTeX source format making it easy to adapt the materials to your needs (if you are a trainer), to translate them and to contribute to them.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Android History

> From: [eLinux.org](http://eLinux.org/Android_History "http://eLinux.org/Android_History")

# Android History

This page has a few tidbits regarding the history of Android

## Contents

*   1 Overview
*   2 2005
*   3 2007
*   4 2008
*   5 2009
*   6 2010

## Overview

*   [Google's Open Source Android OS Will Free the Wireless Web](http://www.wired.com/techbiz/media/magazine/16-07/ff_android?currentPage=all) Wired article, June 2008
    *   Has a description of Rubin and Page's first meeting, and the overall Google strategy for Android
    *   Some additional commentary (see the last 4 paragraphs) are at: [The forgotten history of Android](http://news.netapex.org/?page_id=463)

## 2005

August, 2007 - Google acquires Android Inc.

*   See [Google Buys Android for Its Mobile Arsenal](http://www.businessweek.com/technology/content/aug2005/tc20050817_0949_tc024.htm)
    *   Business Week, August 2005

## 2007

*   See [I, Robot: The Man Behind the Google Phone](http://www.nytimes.com/2007/11/04/technology/04google.html?_r=1)
    *   New York Times article about Andy Rubin, Nov 2007

November, 2007 - Creation of Open Handset Alliance

## 2008

October, 2008 - The first phone, the G1, ships in the U.S.

## 2009

February, 2009 - The [Open Embedded Software Foundation](http://www.oesf.org/) (OESF) is [formed](http://techon.nikkeibp.co.jp/english/NEWS_EN/20090325/167661/) in Japan.

April, 2009 - 1.5 update (codenamed "CupCake")

July, 2009 - [Mentor Graphics acquires Embedded Alley](http://www.linuxfordevices.com/c/a/News/Mentor-Graphics-acquires-Embedded-Alley/)

September, 2009 - [1.6 SDK released](http://www.androidcentral.com/android-donut-16-sdk-released-possibly-coming-users-next-month) (codenamed "Donut")

September-November, 2009 - Motorola, Samsung, HTC, Sony/Ericsson announce new Android phones

October, 2001 - 2.0 SDK released (codenamed "Eclair")

November, 2009 - Google [announces](http://googleblog.blogspot.com/2009/10/announcing-google-maps-navigation-for.html) [Maps Navigation](http://www.google.com/mobile/navigation/index.html#p=default) program

*   Business analysis: [Google Redefines Disruption: the "Less than Free" Business Model](http://abovethecrowd.com/2009/10/29/google-redefines-disruption-the-%E2%80%9Cless-than-free%E2%80%9D-business-model/)

## 2010

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Versions

> From: [eLinux.org](http://eLinux.org/Android_Versions "http://eLinux.org/Android_Versions")

# Android Versions

## Contents

*   1 Versions
    *   1.1 Version 1.1
    *   1.2 Version 1.5 (cupcake)
    *   1.3 Version 1.6 (donut)
    *   1.4 Version 2.1 (eclair)
    *   1.5 Version 2.2 (froyo)
    *   1.6 Version 4.4 (kitkat)
*   2 Fragmentation
    *   2.1 Dealing with API levels
    *   2.2 Need to support multiple versions

## Versions

For a lot more detail, you should see the [wikipedia page for Android version history](http://en.wikipedia.org/wiki/Android_version_history)

The following different Android versions have been released:

| Version number | code name | API Level | Main features |
| --- | --- | --- | --- |
| 1.1 | *unknown* | 2 | The base release, with Android system, phone, camera, webkit-based browser, Google search, contacts, calendar, cloud synchronization, android market, etc. |
| 1.5 | Cupcake | 3 | camcorder mode, video and picture upload to net, auto bluetooth connect, animated screen transitions |
| 1.6 | Donut | 4 | voice search |
| 2.1 | Eclair | 7 | more screen resolutions, live wallpaper, MS exchange support, UI revamp |
| 2.2 | Froyo | 8 | Dalvik JIT, USB tethering, voice dialing, V8 and javascript, adobe flash support |
| 4.4 | KitKat | 19 | Low RAM improvements, sensor batching, full-screen UI, ART experimental test |

See the [wikipedia article](http://en.wikipedia.org/wiki/Android_(operating_system)#Update_history) for some good information about different versions.

### Version 1.1

SDK released February, 2009

### Version 1.5 (cupcake)

SDK released April, 2009

Kernel version: 2.6.27

### Version 1.6 (donut)

SDK released September, 2009

Kernel version: 2.6.29

### Version 2.1 (eclair)

SDK released October, 2009

Kernel version: 2.6.29

### Version 2.2 (froyo)

SDK release May, 2010

Kernel version: 2.6.32

### Version 4.4 (kitkat)

SDK release October 31, 2013

Kernel version: 3.10?

Links to Android 4.4 information

*   Official Android Blog post:
    *   [`officialandroid.blogspot.com/2013/10/android-for-all-and-new-nexus-5.html`](http://officialandroid.blogspot.com/2013/10/android-for-all-and-new-nexus-5.html)
*   Consumer facing highlights:
    *   [`www.android.com/versions/kit-kat-4-4/`](http://www.android.com/versions/kit-kat-4-4/)
*   Platform highlights:
    *   [`developer.android.com/about/versions/kitkat.html`](http://developer.android.com/about/versions/kitkat.html)
*   Android Developers Blog post:
    *   [`android-developers.blogspot.com/2013/10/android-44-kitkat-and-updated-developer.html`](http://android-developers.blogspot.com/2013/10/android-44-kitkat-and-updated-developer.html)
*   API highlights:
    *   [`developer.android.com/about/versions/android-4.4.html`](http://developer.android.com/about/versions/android-4.4.html)
*   SDK updates:
    *   [`developer.android.com/tools/revisions/platforms.html`](http://developer.android.com/tools/revisions/platforms.html)

## Fragmentation

### Dealing with API levels

Developers should specify which API level their application requires or is known to work with.

See [`developer.android.com/guide/appendix/api-levels.html`](http://developer.android.com/guide/appendix/api-levels.html)

### Need to support multiple versions

Dianne Hackborn had this to say about fragmentation, after a developer complained about having to support different versions of Android:

Developers will never be able to rely on there only being one version to target. Never. Please drop that from your mind. This is not the case on Windows, it is not the case on MacOS X, it is not the case on any platform that is not uber-tightly controlled.

That would mean that every user, of every Android device, would be delivered free updates as long as they are using the device. This would completely prevent very interesting Android devices like the Dell Streak (which due to its interesting design requires some customizations to the platform), and would significantly limit the ability of manufacturers to push Android into other interesting places.

This would also very greatly slow down the development of the platform. Every release of Android would need to be extensively QAed on every existing Android device before it could go out to any of them. There are currently > 50 such devices, and that number is growing exponentially. Android just can't scale if that final productization and QA phase is not spread out to the manufacturers. A core part of what makes our Android model work is that most products are owned by their manufacturer.

Here's a very small example: you say that manufacturers would be responsible for the kernel. Obviously, that is required because the kernel needs to talk with whatever their hardware is. However, pretty much every release of the platform has snapped up to a more recent version of the Linux kernel. The QA for that version of the platform is done against that kernel on a small set of specific devices. For what you propose, it would either need to be QAed across all possible kernels, or require that all manufacturers upgrade to the newer kernel before it can go out to any devices.

I honestly think that developers who are demanding there be only one version they need to think about are living in a strange fantasy world. Windows developers have always needed to think about 3 or so active versions of windows. From the stats I have seen there are regularly two very active versions of MacOS X that users run. Heck, if you are a web developer you need to consider different browsers and different versions implementing different versions of web standards.

I can more understand the issues from users, who want to be able to upgrade their current device to a newer version. Even there, though, if you compare us against another company that say releases a major platform update every year... we're not that bad. Look at the devices that have been out... how many at this point haven't received a significant platform update in the year since they came out? I think not many -- very few of them have been around for more than a year, and at this point the bulk of those have received at least one update. The main difference is that Android has gone through 3-4 significant updates in a year... so sure, not every one of those goes to every device, but I do think the manufacturers are showing that they are going to do some work to snap their devices to a more recent version, and I also think that as they are learning about Android and going through this upgrade experience the first few times they are learning how to better handle it.

At the end of the day Android is not a single monolithic uniform environment. That isn't what Android is intended to be, and honestly I think that this is something that should be pretty clear from day one. I consider that one of Android's strengths.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Android Linux 内核

Android 使用 Linux 作为底层的内核，并且对 Linux 做了改造，为智能设备定制了诸多新特性。

# Where to obtain

> From: [eLinux.org](http://eLinux.org/Android_Kernel_Download "http://eLinux.org/Android_Kernel_Download")

# Android Kernel Download

See [`source.android.com/source/building-kernels.html`](http://source.android.com/source/building-kernels.html)

Most of following information is outdated.

## Main Google Android Kernels

The main Google repository with Android source code is at: [`android.googlesource.com/`](https://android.googlesource.com/)

There are (as of July 2014) 4 main separate kernel repositories at that site:

*   common
*   exynos
*   goldfish
*   lk
*   samsung
*   tegra
*   msm
*   omap

To download one of these and use it directly, you can use git. For example: (Do not use the git protocol to clone)

```
git clone https://android.googlesource.com/kernel/common.git kernel 
```

To preserve your sanity, it's probably worth downloading this into a 'kernel' directory in your overall Android source directory scheme

You can use repo, following the instructions at [`source.android.com/download`](http://source.android.com/download), to pull down the entire Android source. However, when you download the rest of the Android source code, using the 'repo' command, you do NOT automatically get a kernel tree included. That is, a kernel git tree is not referenced in the default Android manifest file,

To add projects, such as the kernel, to your overall Android repository scheme, you add the appropriate kernel repository to your local manifest.xml file. This file is located in the .repo directory.

To include the kernel/common tree, include a line like this in .repo/manifest.xml:

```
<project path="kernel/common" name="kernel/common" /> 
```

The complete list of projects (including other kernel options besides kernel/common) is listed on [`android.googlesource.com/`](https://android.googlesource.com/).

Note that the default revision for git repositories is specified in the \ <defaultu0005c class="calibre16">tag in manifest.xml as "revision=master" but the kernel/common repository may not have a head called "master". In that case if you just type "repo sync kernel/common" you may see the message:</defaultu0005c>

```
error: revision master in kernel/common not found 
```

Typically the heads in the kernel/common repository will be called android-2.6.x (where x is the kernel number); specifying this number in the manifest should allow repo to sync properly, i.e.:

```
<project path="kernel/common" name="kernel/common" revision="android-2.6.27"/> 
```

You can view the heads by clicking on the project link from [`android.git.kernel.org/`](http://android.git.kernel.org/).

For more about repo, see [`source.android.com/download/using-repo`](http://source.android.com/download/using-repo)

## Other Repositories with Android-specific changes

*   Linux kernel for omap and beagle-board, by Embinux: [`labs.embinux.org/git/cgit.cgi/repo/kernel.git`](http://labs.embinux.org/git/cgit.cgi/repo/kernel.git)
    *   clone with: git clone git://labs.embinux.org/repo/kernel.git kernel

## 'Raw' Android kernel patches

I do not know of any freely available patches for the Linux kernel with the Android fixes, as of November 2009\. I have, however, heard of multiple efforts to extract the patches to make it easier to port the Android kernel features onto newer Linux kernels.

Here is a way of extracting raw Android patches at a certain point in time, though this may be dated:

```
(Do not use the git protocol to clone the source)
git clone https://android.googlesource.com/kernel/common.git android-kernel
cd android-kernel
git checkout --track -b android-2.6.32 origin/android-2.6.32
git fetch --tags git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux-2.6.32.y.git
git shortlog v2.6.32.9..HEAD
git format-patch v2.6.32.9..HEAD 
```

Sum total 173 patches for the 2.6.32.9 kernel as of writing.

If anyone knows where raw android kernel patches are available, please add a link here. See also the [Android Kernel Features](http://eLinux.org/Android_Kernel_Features "Android Kernel Features") page for more information about individual kernel features.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# How to build

> From: [eLinux.org](http://eLinux.org/Android_Kernel_Build "http://eLinux.org/Android_Kernel_Build")

# Android Kernel Build

How to build an Android kernel.

The Android build system may or may not automatically rebuild a kernel for you.

This page documents how to build an Android kernel, independent of the regular Android "distribution" build system.

[FIXTHIS - nothing here yet.]

## Building an Individual application

Steps required to compile individual application package in Android SDK.

1.  Go to build directory under Android sdk directory
2.  Execute source source envsetup.sh
3.  Go to corresponding application directory
4.  Issue mm command to build the application only

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# How to install (on phone, on emulator, etc.)

> From: [eLinux.org](http://eLinux.org/Android_Kernel_Installation "http://eLinux.org/Android_Kernel_Installation")

# Android Kernel Installation

How to use a new kernel with the emulator or on a product.

In general, this will be very product-specific.

## Contents

*   1 With the G1/ADP1
    *   1.1 Installing a boot image
*   2 With the emulator
*   3 On a board using an SDCARD
    *   3.1 OMAP EVM

## With the G1/ADP1

On the ADP1, you can boot using a specific kernel using fastboot, which will send it to the board as part of the bootup sequence. Specify the kernel as one of the parameters to the 'fastboot boot' option. See [`www.gotontheinter.net/content/fastboot-cheat-sheet`](http://www.gotontheinter.net/content/fastboot-cheat-sheet) for details.

### Installing a boot image

You can create a "boot" image directly, and install that to the boot or recovery flash partition manually. This can be done either using a running kernel or using fastboot.

See these instructions for doing that: [`android-dls.com/wiki/index.php?title=HOWTO:_Unpack,_Edit,_and_Re-Pack_Boot_Images`](http://android-dls.com/wiki/index.php?title=HOWTO:_Unpack,_Edit,_and_Re-Pack_Boot_Images)

Also, you can install the kernel into a recovery image, and install the recovery image using the system recovery procedure.

## With the emulator

You can specify the kernel to use with the emulator using the '-kernel' command line option.

There are some interesting notes about using the emulator without specifying a AVD file at: [`groups.google.com/group/android-platform/browse_thread/thread/591290fde1e9865a`](http://groups.google.com/group/android-platform/browse_thread/thread/591290fde1e9865a)

## On a board using an SDCARD

### OMAP EVM

For an OMAP EVM, using the Android image available from Mistral, place a new uImage on the SDCARD at the root the vfat partition. The bootloader will run the commands (from where??) to load the uImage and boot it.

Note that for the OMAP EVM, you need to have the board configured to boot from sdcard, rather than from flash.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# What version to use

> From: [eLinux.org](http://eLinux.org/Android_Kernel_Versions "http://eLinux.org/Android_Kernel_Versions")

# Android Kernel Versions

## Contents

*   1 Kernel versions known to run Android
    *   1.1 2.6.23
    *   1.2 2.6.25
    *   1.3 2.6.26
    *   1.4 2.6.29
    *   1.5 2.6.32
    *   1.6 2.6.35
*   2 Selecting a kernel tree for a new port
*   3 Android git kernel trees

## Kernel versions known to run Android

### 2.6.23

The first-generation Google-TV products (released about November, 2010) used kernel version 2.6.23\. It is speculated that this is due to existing binary driver support for the Intel chipsets used in those products.

### 2.6.25

The original (version 1.0) of Android for the G1/ADP1 used Linux version 2.6.25

### 2.6.27

The 1.5 release of Android (cupcake) for the G1/ADP1 used Linux version 2.6.27

### 2.6.29

As of September, 2009, the kernel/common.git tree for Android has a 2.6.29 kernel. Donut uses this kernel.

### 2.6.32

As of July 2010, the kernel/common.git tree for Android has a 2.6.32 kernel. This kernel is used by Froyo.

### 2.6.35

Gingerbread used kernel version 2.6.35.

## Selecting a kernel tree for a new port

This article has good information on porting Android to a new device. Note that it debates which kernel tree is appropriate to start with, for porting to a new device:

*   [`www.linuxfordevices.com/c/a/Linux-For-Devices-Articles/Porting-Android-to-a-new-device/`](http://www.linuxfordevices.com/c/a/Linux-For-Devices-Articles/Porting-Android-to-a-new-device/)

## Android git kernel trees

As of July 2010, the [android repository on kernel.org](http://android.git.kernel.org/) had the following kernel trees:

kernel/common.git This is the kernel tree that matches what is put into official Android products by Google or its partners

kernel/experimental.git some experimental stuff

kernel/linux-2.6.git mirror of Linus' kernel tree. This is a reference for the kernel tree used in Android

kernel/lk.git not a kernel tree, but a bootloader

kernel/msm.git kernel for MSM (Qualcomm chip used in many HTC products)

kernel/omap.git mirror of kernel tree maintained by texas instruments., supporting OMAP chips. usually based on kernel.org latest version, android products w OMAP chip can use this kernel port.

kernel/tegra.git nvidia kernel tree

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Kernel features

> From: [eLinux.org](http://eLinux.org/Android_Kernel_Features "http://eLinux.org/Android_Kernel_Features")

# Android Kernel Features

## Contents

*   1 Kernel features unique to Android
    *   1.1 Resources
    *   1.2 Temporary including in mainline 'staging'
    *   1.3 Android mainlining project
*   2 List of kernel features unique to Android
    *   2.1 Binder
    *   2.2 ashmem
    *   2.3 pmem
    *   2.4 logger
    *   2.5 wakelocks
    *   2.6 oom handling
    *   2.7 Alarm timers
        *   2.7.1 POSIX Alarm Timers
    *   2.8 paranoid network security
    *   2.9 timed output / timed gpio
    *   2.10 RAM-CONSOLE
    *   2.11 other kernel changes
*   3 Kernel configuration options

## Kernel features unique to Android

In the course of development, Google developers made some changes to the Linux kernel. The amount of changes is not extremely large, and is on the order of changes that are customarily made to the Linux kernel by embedded developers (approximately 250 patches, with about 3 meg. of differences in 25,000 lines). The changes include a variety of large and small additions, ranging from the wholesale addition of a flash filesystem (YAFFS2), to very small patches to augment Linux security (paranoid networking patches).

Various efforts have been made over the past few years to submit these to changes to mainline (mostly by Google engineers, but also by others), with not much success so far.

### Resources

A very good overview of the changes is available in a talk by John Stultz at ELC 2011\. (The talk has a somewhat misleading name.)

*   [Android OS for Servers](http://eLinux.org/images/8/89/Elc2011_stultz.pdf "Elc2011 stultz.pdf")- John Stultz, ELC 2011
    *   This talks breaks down the differences between an Android Linux kernel and a stock Linux kernel, and provides information about the features of each.
*   [`www.lindusembedded.com/blog/2010/12/07/android-linux-kernel-additions/`](http://www.lindusembedded.com/blog/2010/12/07/android-linux-kernel-additions/)
    *   Lindus Embedded (Alex Gonzalez) has a listing of kernel changes based on an Android kernel for the Freescale MX51 SOC, with some good information about each change.
*   [`yidonghan.wordpress.com/2010/01/28/porting-android-to-a-new-device/`](http://yidonghan.wordpress.com/2010/01/28/porting-android-to-a-new-device/)
    *   Peter McDermott's excellent description of his work to port Android to the Nokia N810.
    *   Also, see his annotated list of modified and added kernel files, at: [`www.linuxfordevices.com/files/misc/porting-android-to-a-new-device-p3.html`](http://www.linuxfordevices.com/files/misc/porting-android-to-a-new-device-p3.html)
    *   See the project site [at sourceforge](http://android-n810.sourceforge.net/).
*   [`www.slideshare.net/jollen/android-os-porting-introduction`](http://www.slideshare.net/jollen/android-os-porting-introduction)
    *   Jollen Chen's excellent presentation on system-level Android features, including an overview of kernel features unique to Android: *Note: Parts of the presentation are in Chinese*

### Temporary including in mainline 'staging'

Some changes were temporarily added at the "staging" driver area in the stock kernel, but were removed due to lack of support. See [Greg KH blog post on -staging for 2.6.33](http://www.kroah.com/log/linux/staging-status-12-2009.html), where he announces the **removal** of various Android drivers from -staging.

### Android mainlining project

Several groups and individuals are working to get kernel changes from Android mainlined into the Linux kernel.

Please see [Android Mainlining Project](http://eLinux.org/Android_Mainlining_Project "Android Mainlining Project") for more information.

## List of kernel features unique to Android

Here is a list of changes/addons that the Android Project made to the linux kernel. As of September, 2011, these kernel changes are not part of the standard kernel and are only available in the Android kernel trees in the Android Open Source project.

This list does not include board- or platform-specific support or drivers (commonly called "board support").

### Binder

Binder is an Android-specific interprocess communication mechanism, and remote method invocation system.

See [Android Binder](http://eLinux.org/Android_Binder "Android Binder")

### ashmem

*   ashmem - Android shared memory
    *   implementation is in `mm/ashmem.c`

According to the Kconfig help "The ashmem subsystem is a new shared memory allocator, similar to POSIX SHM but with different behavior and sporting a simpler file-based API."

Apparently it better-supports low memory devices, because it can discard shared memory units under memory pressure.

To use this, programs open /dev/ashmem, use mmap() on it, and can perform one or more of the following ioctls:

*   ASHMEM_SET_NAME
*   ASHMEM_GET_NAME
*   ASHMEM_SET_SIZE
*   ASHMEM_GET_SIZE
*   ASHMEM_SET_PROT_MASK
*   ASHMEM_GET_PROT_MASK
*   ASHMEM_PIN
*   ASHMEM_UNPIN
*   ASHMEM_GET_PIN_STATUS
*   ASHMEM_PURGE_ALL_CACHES

From a thread on android-platform [source](http://groups.google.com/group/android-platform/browse_thread/thread/2fa6ba5ef9f81d22/0b91a39d108d02fc)

You can create a shared memory segment using:

```
 fd = ashmem_create_region("my_shm_region", size);
    if(fd < 0)
        return -1;
    data = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
    if(data == MAP_FAILED)
        goto out; 
```

In the second process, instead of opening the region using the same name, for security reasons the file descriptor is passed to the other process via binder IPC.

The libcutils interface for ashmem consists of the following calls: (found in system/core/include/cutils/ashmem.h)

*   int ashmem_create_region(const char *name, size_t size);
*   int ashmem_set_prot_region(int fd, int prot);
*   int ashmem_pin_region(int fd, size_t offset, size_t len);
*   int ashmem_unpin_region(int fd, size_t offset, size_t len);
*   int ashmem_get_size_region(int fd);

### pmem

*   PMEM - Process memory allocator
    *   implementation at: `drivers/misc/pmem.c` with include file at: `include/linux/android_pmem.h`
    *   Brian Swetland says:

```
The pmem driver is used to manage large (1-16+MB) physically contiguous
regions of memory shared between userspace and kernel drivers (dsp, gpu,
etc).  It was written specifically to deal with hardware limitations of
the MSM7201A, but could be used for other chipsets as well.  For now,
you're safe to turn it off on x86. 
```

David Sparks wrote the following: [source](http://groups.google.com/group/android-framework/msg/06bb9d8c294ce6d7)

```
2\. ashmem and pmem are very similar. Both are used for sharing memory
between processes. ashmem uses virtual memory, whereas pmem uses
physically contiguous memory. One big difference is that with ashmem,
you have a ref-counted object that can be shared equally between
processes. For example, if two processes are sharing an ashmem memory
buffer, the buffer reference goes away when both process have removed
all their references by closing all their file descriptors. pmem
doesn't work that way because it needs to maintain a physical to
virtual mapping. This requires the process that allocates a pmem heap
to hold the file descriptor until all the other references are closed.

3\. You have the right idea for using shared memory. The choice between
ashmem and pmem depends on whether you need physically contiguous
buffers. In the case of the G1, we use the hardware 2D engine to do
scaling, rotation, and color conversion, so we use pmem heaps. The
emulator doesn't have a pmem driver and doesn't really need one, so we
use ashmem in the emulator. If you use ashmem on the G1, you lose the
hardware 2D engine capability, so SurfaceFlinger falls back to its
software renderer which does not do color conversion, which is why you
see the monochrome image. 
```

### logger

*   logger - system logging facility
    *   This is the kernel support for the 'logcat' command
    *   The kernel driver for the serial devices for logging are in the source code `drivers/misc/logger.c`
    *   See [Android logger](http://eLinux.org/Android_logger "Android logger") for more information about the kernel code
    *   See Android Logging System for an overview of the system it supports

### wakelocks

*   wakelock - used for power management files `kernel/power/wakelock.c`
    *   Holds machine awake on a per-event basis until wakelock is released
    *   See Android Power Management for detailed information

### oom handling

*   oom handling modifications
    *   lowmem notifications
    *   implementation at: `drivers/misc/lowmemorykiller.c`
    *   also at: `security/lowmem.c`

Informally known as the Viking Killer, the OOM handler simply kills processes as available memory becomes low. The kernel module follows rules for this that are supplied from user space in two ways:

1.  init writes information about memory levels and associated classes:

2.  The write value must be consistent with the above properties.

3.  Note that the driver only supports 6 slots, so we have combined some of the classes into the same memory level; the associated processes of higher classes will still be killed first.
    *   From /init.rc:

```
 write /sys/module/lowmemorykiller/parameters/adj 0,1,2,4,7,15
   write /sys/module/lowmemorykiller/parameters/minfree 2048,3072,4096,6144,7168,8192 
```

1.  User space sets the oom_adj of processes to put them in the correct class for their current operation. This redefines the meaning of oom_adj from that used by the standard OOM killer to something that is more aggressive and controlled.

These oom_adj levels end up being based on the process lifecycle defined here: [`developer.android.com/guide/topics/fundamentals.html#proclife`](http://developer.android.com/guide/topics/fundamentals.html#proclife)

### Alarm timers

This is the kernel implementation to support Android's AlarmManager. It lets user space tell the kernel when it would like to wake up, allowing the kernel to schedule that appropriately and come back (holding a wake lock) when the time has expired regardless of the sleep state of the CPU.

#### POSIX Alarm Timers

Note that POSIX Alarm timers, which implement this functionality (but not identically), was accepted into mainline Linux in kernel version 3.0.

See [Waking Systems from Suspend](https://lwn.net/Articles/429925/) and [`lwn.net/Articles/439364/`](http://lwn.net/Articles/439364/)

### paranoid network security

*   paranoid network security
    *   See [Android_Security#Paranoid_network-ing](http://eLinux.org/Android_Security#Paranoid_network-ing "Android Security")

### timed output / timed gpio

Generic gpio is a mechanism to allow programs to access and manipulate gpio registers from user space.

Timed output/gpio is a system to allow chaning a gpio pin and restore it automatically after a specified timeout. See `drives/misc/timed_output.c` and `drives/misc/timed_gpio.c` This expose a user space interface used by the vibrator code.

On ADP1, there is a driver at:

```
# cd /sys/bus/platform/drivers/timed-gpio
# ls -l
--w-------    1 0        0            4096 Nov 13 02:11 bind
lrwxrwxrwx    1 0        0               0 Nov 13 02:11 timed-gpio -> ../../../../devices/platform/timed-gpio
--w-------    1 0        0            4096 Nov 13 02:11 uevent
--w-------    1 0        0            4096 Nov 13 02:11 unbind 
```

Also, there is a device at:

```
# cd /sys/devices/platform/timed-gpio
# ls -lR
.:
lrwxrwxrwx    1 0        0               0 Nov 13 01:34 driver -> ../../../bus/platform/drivers/timed-gpio
-r--r--r--    1 0        0            4096 Nov 13 01:34 modalias
drwxr-xr-x    2 0        0               0 Nov 13 01:34 power
lrwxrwxrwx    1 0        0               0 Nov 13 01:34 subsystem -> ../../../bus/platform
-rw-r--r--    1 0        0            4096 Nov 13 01:34 uevent

./power:
-rw-r--r--    1 0        0            4096 Nov 13 01:34 wakeup 
```

### RAM_CONSOLE

This allows saving the kernel printk messages to a buffer in RAM, so that after a kernel panic they can be viewed in the next kernel invocation, by accessing /proc/last_kmsg.

[Would be good to get more details on how to set this up and use it here!] [I guess this is something like pramfs?]

### other kernel changes

Here is a miscellaneous list of other kernel changes in the mistral Android kernel:

*   switch events - drivers/switch/* userspace support for monitoring GPIO via sysfs/uevent used by vold to detect USB
*   USB gadget driver for ADB - drivers/usb/gadget/android.c
*   yaffs2 flash filesystem
*   support in FAT filesystem for FVAT_IOCTL_GET_VOLUME_ID
*   and more...

## Kernel configuration options

The file [Documentation/android.txt](http://android.git.kernel.org/?p=kernel/common.git;a=blob_plain;f=Documentation/android.txt;hb=HEAD) has a list of required configuration options for a kernel to support an Android system.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Board Support highlights

> From: [eLinux.org](http://eLinux.org/Android_Board_Support "http://eLinux.org/Android_Board_Support")

# Android Board Support

Porting Android to a new platform can be a challenge. Here are some resources to start with that:

## Contents

*   1 Processor Support
    *   1.1 ARM
        *   1.1.1 OMAP
    *   1.2 Mips
    *   1.3 x86
*   2 Individual Platform Support
    *   2.1 For Nexus One
        *   2.1.1 Unlocking the phone
        *   2.1.2 Serial port access
*   3 System Requirements
    *   3.1 RAM (>256M if possible)

## Processor Support

### ARM

Most ports of Android are to ARM-based platforms.

#### OMAP

*   Mentor Graphics and Texas Instruments support Android on OMAP processors via the [project](http://code.google.com/p/rowboat/%7CRowboat)
*   See also [Android on OMAP](http://eLinux.org/Android_on_OMAP "Android on OMAP"), which has a very thorough listing of issues faced in initially porting Android to OMAP

### Mips

Mentor Graphics did a port of Android to MIPS.

See [`www.mipsandroid.org`](http://www.mipsandroid.org)

(Unfortunately, this site requires registration.)

*   MIPS now has support for SMP on Android - see [MIPS Supports Symmetric Multiprocessing on Android Platform](http://edageek.com/2010/06/01/smartphones-smp/) - Posted by Ken Cheung in IP Cores on Tuesday, June 1, 2010

### x86

There is a whole well-developed project for Android on x86.

See [`www.android-x86.org/`](http://www.android-x86.org/)

At least one major product (Sony Internet TV) is reported to be x86-based. Intel has a team of developers working on Android issues. See Mark Gross' presentation from ELC 2010 for some tips from them about using Android

*   [Experiences in Android Porting, Lessons Learned,Tips and Tricks](http://eLinux.org/images/e/ee/ELC2010-android-xp-tips-tricks.pdf "ELC2010-android-xp-tips-tricks.pdf") by Mark Gross, April 2010, Embedded Linux Conference 2010

*   Intel is working on "native" Android support. See [Intel prepping x86 port for Android 2.2](http://www.linuxfordevices.com/c/a/News/Intel-x86-port-and-Sprint-upgrade-plans/?kc=LNXDEVNL063010) By Eric Brown, lwn.net, 2010-06-28

## Individual Platform Support

### For Nexus One

#### Unlocking the phone

Bryan Swetland says: ([here](http://torvalds-family.blogspot.com/2010/02/happy-camper.html?showCom))

All Nexus One devices have an unlockable bootloader (% fastboot oem unlock), which, once unlocked will allow you to reflash the boot partition (kernel + ramdisk), system partition, etc.

Full kernel sources are available here: [`android.git.kernel.org/?p=kernel/msm.git;a=shortlog;h=refs/heads/android-msm-2.6.29-nexusone`](http://android.git.kernel.org/?p=kernel/msm.git;a=shortlog;h=refs/heads/android-msm-2.6.29-nexusone) (make mahimahi_defconfig to configure just like the production kernel). We're in the process of rebasing up to .32, on our way to .33 and beyond.

#### Serial port access

There are some serial port pins available on the micro-USB connector, which you can access if you have the right hardware.

See this [discussion on xda-developers](http://forum.xda-developers.com/showthread.php?t=625434) for detailed information and links.

Brian Swetland says: TTL level (~3.3v?) serial is present on the D+/D- pins of the micro USB connector whenever VBUS (usb +5v power) is not present. This is physical UART1 (ttyMSM0). In standard builds the FIQ kernel debugger runs there. You'll have to disable the FIQ debugger and enable the serial device in your kernel config if you want to use it as a regular serial port.

## System Requirements

### RAM (>256M if possible)

Dianne Hackborn had this to say (in August, 2009) about RAM requirements for Android:

I would recommend at least 128MB available to the *kernel*. In many architectures, a big chunk of RAM will be dedicated to the radio, so you need to take that into account, and the 128MB recommendation does not cover that. Also if your architecture allocates graphics surfaces in user space, bump it up by 16MB or so (The Qualcomm devices I have experience do their allocations in RAM outside of that accessible to the kernel.) And of course this also depends on the size and density of your screen, camera megapixels, etc. If the screen has more pixels than 320x480 or the camera is more than 3 megapixels, bump the size up accordingly.

For reference, the myTouch has 192MB of RAM which on 1.6 only left 100MB left for the kernel and user space, and was *very* tight on RAM. Don't go that low. An update to the Qualcomm radio apparently frees up a bunch of space, adding over 10MB or so to user space... that should run even 2.2 okay. At this level, though, the amount of RAM is probably the most important aspect to how well the device will run. Don't skip on RAM, and you'll have a much better running device, with a lot fewer headaches as you try to get everything working well. That is from painful experience. :)

Another reference point -- the Droid has 256MB RAM, which runs the system well, but it also does its graphics allocations in user space and has a high density screen so you can still end up not keeping as many processes running as you'd like if loading large pages with the browser, running lots of background services, etc.

The Nexus One has 512MB of RAM and honestly that is really more than we know what to do with. It is great. :) I ended up putting some code into the activity manager to put a hard limit on the number of processes we would keep around, because there was so much memory we had often could keep way more processes than was useful. That was never an issue on Droid. ;)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Android 系统信息

讨论 Android 系统部分，内容涵盖系统引导、电源管理、系统安全、Dalvik Java 虚拟机、包管理、网络、文件系统和日志系统。

# Booting

> From: [eLinux.org](http://eLinux.org/Android_Booting "http://eLinux.org/Android_Booting")

# Android Booting

The bootup of an Android system consists of several phases, which are outlined here.

## Contents

*   1 Key bootup components
    *   1.1 Bootloader
    *   1.2 'init'
        *   1.2.1 'init' resources
*   2 Sequence of boot steps on ADP1
    *   2.1 firmware
    *   2.2 kernel
    *   2.3 user space
*   3 Tools for analyzing Android Bootup
*   4 Notes on the Android startup procedure
    *   4.1 Overview
    *   4.2 strace
    *   4.3 Interaction of different processes on application initialization
    *   4.4 Improving Bootup Time presentation
*   5 News
    *   5.1 1-second boot of Android

## Key bootup components

### Bootloader

The first program which runs on any Android system is the bootloader. Technically, the bootloader is outside the realm of Android itself, and is used to do very low-level system initialization, before loading the Linux kernel. The kernel then does the bulk of hardware, driver and file system initialization, before starting up the user-space programs and applications that make up Android.

Often, the first-stage bootloader will provide support for loading recovery images to the system flash, or performing other recovery, update, or debugging tasks.

The bootloader on the ADP1 detects certain keypresses, which can be used to make it load a 'recovery' image (second instance of the kernel and system), or put the phone into a mode where the developer can perform development tasks ('fastboot' mode), such as re-writing flash images, directly downloading and executing an alternate kernel image, etc.

### 'init'

A key component of the Android bootup sequence is the program 'init', which is a specialized program for initializing elements of the Android system. Unlike other Linux systems (embedded or otherwise), Android uses its own initialization program. (Linux desktop systems have historically used some combination of /etc/inittab and sysV init levels - e.g. /etc/rc.d/init.d with symlinks in /etc/rc.d/rc.[2345]). Some embedded Linux systems use simplified forms of these -- such as the init program included in busybox, which processes a limited form of /etc/inittab, or a direct invocation of a shell script or small program to do fixed initialization steps.

The Android 'init' program processes two files, executing the commands it finds in them, called 'init.rc' and 'init.\<machineu0005c_nameu0005c class="calibre16">.rc', where \ <machineu0005c_nameu0005c class="calibre16">is the name of the hardware that Android is running on. (Usually, this is a code word. The name of the HTC1 hardware for the ADP1 is 'trout', and the name of the emulator is 'goldfish'.</machineu0005c_nameu0005c></machineu0005c_nameu0005c>

The 'init.rc' file is intended to provide the generic initialization instructions, while the 'init.\<machineu0005c_nameu0005c class="calibre16">.rc' file is intended to provide the machine-specific initialization instructions.</machineu0005c_nameu0005c>

#### 'init' resources

The syntax for these .rc files is documented in a readme file in the source tree. See the [Android init language reference](https://android.googlesource.com/platform/system/core/+/master/init/readme.txt)

Or, see also: [kandroid copy of old Android PDK](http://www.kandroid.org/online-pdk/guide/bring_up.html)

*   Note that the old PDK has been retracted from where it used to found below [`source.android.com/porting`](http://source.android.com/porting)
    *   You may be able to reconstruct it though:
        *   You need a local copy of the AOSP sourcetree, and run the usual *build/envsetup.sh* preparation
        *   Use *repo init -b* to check out the AOSP sourcetree with a tag around 2.3, then *make sdk sdk_all*
        *   This worked for me, though I used some tag around 4.0.4. Some links had to be fixed in the resulting html output

See also [`www.androidenea.com/2009/08/init-process-and-initrc.html`](http://www.androidenea.com/2009/08/init-process-and-initrc.html)

## Sequence of boot steps on ADP1

### firmware

*   first-stage bootloader runs
    *   it detects if a special key is held, and can launch the recovery image, or the 'fastboot' bootloader
*   eventually, a kernel is loaded into RAM (usually with an initrd)
    *   normally, this will be the kernel from the 'boot' flash partition.

### kernel

*   the kernel boots
    *   core kernel initialization
        *   memory and I/O areas are initialized
        *   interrupts are started, and the process table is initialized
    *   driver initialization
    *   kernel daemons (threads) are started
    *   root file system is mounted
    *   the first user-space process is started
        *   usually /init (note that other Linux systems usually start /sbin/init)

### user space

*   the kernel runs /init
    *   /init processes /init.rc and /init.\<machineu0005c_nameu0005c class="calibre16">.rc</machineu0005c_nameu0005c>
    *   dalvik VM is started (zygote). See [Android Zygote Startup](http://eLinux.org/Android_Zygote_Startup "Android Zygote Startup")
    *   several daemons are started:
        *   rild - radio interface link daemon
        *   vold - volume daemon (media volumes, as in file systems - nothing to do with audio volume)
*   the system_server starts, and initializes several core services
    *   See [`www.androidenea.com/2009/07/system-server-in-android.html`](http://www.androidenea.com/2009/07/system-server-in-android.html)
    *   initalization is done in 2 steps:
        *   1) a library is loaded to initialize interfaces to native services, then
        *   2) java-based core services are initialized in ServerThread::run() in [SystemServer.java](http://android.git.kernel.org/?p=platform/frameworks/base.git;a=blob;f=services/java/com/android/server/SystemServer.java)
*   the activity manager starts core applications (which are themselves dalvik applications)
    *   com.android.phone - phone application
    *   android.process.acore - home (desktop) and a few core apps.
*   other processes are also started by /init, somewhere in there:
    *   adb
    *   mediaserver
    *   dbus-daemon
    *   akmd

## Tools for analyzing Android Bootup

*   logcat - see Android Logging System
    *   try this command: 'adb logcat -d -b events | grep "boot"

```
01-01 00:00:08.396 I/boot_progress_start(  754): 12559
01-01 00:00:13.716 I/boot_progress_preload_start(  754): 17879
01-01 00:00:24.380 I/boot_progress_preload_end(  754): 28546
01-01 00:00:25.068 I/boot_progress_system_run(  768): 29230
01-01 00:00:25.536 I/boot_progress_pms_start(  768): 29697
01-01 00:00:25.958 I/boot_progress_pms_system_scan_start(  768): 30117
01-01 00:00:40.005 I/boot_progress_pms_data_scan_start(  768): 44171
01-01 00:00:45.841 I/boot_progress_pms_scan_end(  768): 50006
01-01 00:00:46.341 I/boot_progress_pms_ready(  768): 50505
01-01 00:00:49.005 I/boot_progress_ams_ready(  768): 53166
01-01 00:00:52.630 I/boot_progress_enable_screen(  768): 56793 
```

*   *   or this: 'adb logcat -d | grep preload'

```
10-15 00:00:17.748 I/Zygote  (  535): ...preloaded 1873 classes in 2438ms.
10-15 00:00:17.764 I/Zygote  (  535): ...preloaded 0 resources in 0ms.
10-15 00:00:17.772 I/Zygote  (  535): ...preloaded 15 resources in 7ms. 
```

*   Bootchart - see [Using Bootchart on Android](http://eLinux.org/Using_Bootchart_on_Android "Using Bootchart on Android")
*   strace is pretty handy also, to see the timings for system calls from a process as it runs
    *   You can use strace as a wrapper for a program in init.rc, and save the results to a file
    *   Use -f to follow sub-processes
    *   Use -tt to get detailed timestamps for syscalls
    *   Use -o to output the data to a file

Here is an example of using strace to follow the startup of zygote, and the apps that are forked from it.

Replace:

```
service zygote /system/bin/app_process -Xzygote /system/bin --zygote --start-system-server 
```

with

```
service zygote /system/xbin/strace -f -tt -o /cache/debug/boot.strace /system/bin/app_process -Xzygote /system/bin --zygote --start-system-server 
```

Here is some sample data:

```
$ head boot.strace
571   00:00:11.389939 execve("/system/bin/app_process", ["/system/bin/app_process", "-Xzygote", "/system/bin", "--zygote", "--start-system-server"], [/* 15 vars */]) = 0
571   00:00:11.658878 brk(0)            = 0x804b000
571   00:00:11.659048 mmap2(NULL, 4096, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x77f9a000
571   00:00:11.659169 readlink("/proc/self/exe", "/system/bin/app_process", 4096) = 23
571   00:00:11.659339 access("/etc/ld.so.preload", R_OK) = 0
571   00:00:11.659440 open("/etc/ld.so.preload", O_RDONLY) = 3
571   00:00:11.659548 fstat64(0x3, 0x7fa76650) = 0
571   00:00:11.659887 mmap2(NULL, 36, PROT_READ|PROT_WRITE, MAP_PRIVATE, 3, 0) = 0x77f99000
571   00:00:11.659970 close(3)          = 0
571   00:00:11.660071 open("/lib/libc_sse.so", O_RDONLY) = 3 
```

Please note that writing the strace data takes extra time. For long sequences of very fast syscalls (such as when the timezone file is being read) the overhead of strace itself exaggerates the timings in the trace. Use the timing information with caution.

## Notes on the Android startup procedure

### Overview

See "Android Initialization Process" at: [`blog.chinaunix.net/u2/85805/showart_1421736.html`](http://blog.chinaunix.net/u2/85805/showart_1421736.html) (this address is not work), using [`blog.chinaunix.net/space.php?uid=7788581&do=blog&id=2558375`](http://blog.chinaunix.net/space.php?uid=7788581&do=blog&id=2558375) instead.

### strace

[`benno.id.au/blog/2007/11/18/android-runtime-strace`](http://benno.id.au/blog/2007/11/18/android-runtime-strace)

### Interaction of different processes on application initialization

Talking about Android Process - [`blog.csdn.net/mawl2002/archive/2009/06/24/4295905.aspx`](http://blog.csdn.net/mawl2002/archive/2009/06/24/4295905.aspx)

### Improving Bootup Time presentation

See [Improving Android Boot Time](http://eLinux.org/Improving_Android_Boot_Time "Improving Android Boot Time") - notes and material for a talk at LinuxCon North America, 2010 by Tim Bird

## News

### 1-second boot of Android

Ubiquitous Corporation has announced boot of ARM-based Android system in 1 second. Actually, it's more like a suspend and resume than a boot. See [`www.linuxfordevices.com/c/a/News/Ubiquitous-QuickBoot/?kc=LNXDEVNL032410`](http://www.linuxfordevices.com/c/a/News/Ubiquitous-QuickBoot/?kc=LNXDEVNL032410) [March, 2010]

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Power Management

> From: [eLinux.org](http://eLinux.org/Android_Power_Management "http://eLinux.org/Android_Power_Management")

# Android Power Management

Notes on Power Management in Android

## Contents

*   1 wakelocks
    *   1.1 Creating a wakelock
    *   1.2 Using a wakelock inside the kernel
    *   1.3 Using a wakelock from user space
    *   1.4 Sample 'cat /proc/wakelocks' output
*   2 Patch submission controversy
*   3 Wakelock documentation (from patch)
*   4 Motorola quickwakeup feature
*   5 Earlysuspend
*   6 May 2010 patch submission - "Suspend blockers"

## wakelocks

The first version of Android utilized a system called "wakelocks", which was a set of patches to the Linux kernel to allow a caller to prevent the system from going to low power state.

Each wakelock is defined with a name and type. The type is one of:

*   WAKE_LOCK_IDLE, or
*   WAKE_LOCK_SUSPEND.

The name is an arbitrary ASCII string.

When a wake lock of type "IDLE" is in effect, the system will not enter idle (low power) state, and this should make the device more responsive. That is, it does not have to wake up from idle to respond to interrupts or events. When a wake lock of type "SUSPEND" is held, then the system will not suspend, which takes even longer to resume from.

You can, from user space, see the currently defined wakelocks and a bunch of information about their status, using 'cat /proc/wakelocks'

Below is an example of output from that command.

To see the code for this, see the file:

```
drivers/android/power.c 
```

NOTE: See the [Discussion](http://eLinux.org/Talk:Android_Power_Management "Talk:Android Power Management") page for a note on this file.

### Creating a wakelock

Kernel code can define a wakelock, and get a handle to it, by calling:

```
#include <linux/wakelock.h>
wake_lock_init(struct wakelock *lock, int type, const char *name); 
```

From user space, a process can write a name to

```
/sys/power/wake_lock 
```

to create and lock a suspend lock with that name.

### Using a wakelock inside the kernel

Kernel code can acquire and release the lock with one of the following:

```
void wake_lock(struct wake_lock *lock);
void wake_unlock(struct wake_lock *lock); 
```

Kernel code can also set a timeout to specify automatic release of the wakelock after a specific period, with:

```
void wake_lock_timeout(struct wake_lock *lock, long timeout); 
```

The wakelock does not need to be held prior to calling this (it will automatically lock the wakelock and register the timeout).

### Using a wakelock from user space

To release a suspend wake lock from user space, a process can write the lock name to: /sys/power/wake_unlock

### Sample 'cat /proc/wakelocks' output

Here are some wakelocks from my ADP1 phone, running Donut (I think):

*Note: I widened the columns and adjusted the title row to make the columns line up better.*

```
 $ cat /proc/wakelocks
name                    count   expire_count  active_since
                                        wake_count          total_time      sleep_time      max_time        last_change
"PowerManagerService"   3580    0       0     0             1706674438454   1354421173104   62626251221     31701936996687
"mddi_link_active_idle_lock" 4641 0     0     0             26749877925     0               253234863       31701903732527
"msmfb_idle_lock"       2549    0       0     0             28889923076     0               266601563       31701902633894
"SMD_RPCCALL"           8913    0       34    0             9038391159      6843658471      176025391       31690316543807
"audio_pcm_idle"        12      0       0     0             68230224609     0               27459259033     31690315567244
"audio_pcm"             12      0       0     0             68230285646     12423248292     27459289551     31690315567244
"rpc_read"              60      0       0     0             3112792         823974          91553           31690315048445
"adsp"                  12      0       0     0             67035552978     11988464355     27387664794     31690282791365
"rpc_read"              12      0       0     0             671387          213624          91553           31686582229842
"usb_mass_storage"      0       0       0     0             0               0               0               0
"rpc_read"              137     0       0     0             8972160         6744383         122070          31685788467635
"rpc_server"            277     0       0     0             406372060       320922839       41412354        31685787887801
"rpc_read"              277     0       0     0             117919940       111480725       94848633        31685787826766
"alarm"                 1220    0       0     0             27851074219     25716247547     382141114       31669270950545
"rpc_read"              4909    0       0     0             375915506       347930899       16021729        31376427078475
"gpio_input"            0       0       0     0             0               0               0               0
"mddi_idle_lock"        54      0       0     0             8752532958      0               249603271       31154190719832
"SMD_DATA5"             862     862     377   0             620518382572    567607416986    6926910400      31153421737899
"alarm_rtc"             177     10      0     0             47826019299     47825439463     1801544190      31152471695175
"KeyEvents"             13271   0       0     0             11482665934     343322752       503204346       30525886245957
"evdev"                 4437    0       0     0             4980529833      98724365        506927490       30525884811631
"evdev"                 188     0       6     0             3093719493      2680358890      393676758       30522918777697
"rpc_read"              18      0       0     0             1464840         0               335693          30520425552599
"gpio_kp"               68      0       14    0             14835723878     2092010498      933135987       30445059005968
"evdev"                 52      0       0     0             98999026        0               24139405        30438128067247
"rpc_read"              596     0       0     0             745910654       211212166       491180420       30046209060900
"rpc_read"              1331    0       0     0             286651593       169342025       112518310       30046203598253
"rpc_read"              10      0       0     0             1495361         427247          274658          3596980453801
"qmi0"                  2       2       0     0             996235351       0               501717529       173250290527
"qmi2"                  1       1       0     0             490385742       0               490385742       173250290527
"qmi1"                  1       1       0     0             493193359       0               493193359       173250290527
"evdev"                 0       0       0     0             0               0               0               0
"evdev"                 0       0       0     0             0               0               0               0
"rpc_read"              2       0       0     0             427247          0               274659          7461350097
"mt9t013"               0       0       0     0             0               0               0               0
"gpio_input"            0       0       0     0             0               0               0               0
"gpio_input"            0       0       0     0             0               0               0               0
"SMD_DATA7"             0       0       0     0             0               0               0               0
"SMD_DATA6"             0       0       0     0             0               0               0               0
"msm_serial_hs_rx"      0       0       0     0             0               0               0               0
"unknown_wakeups"       0       0       0     0             0               0               0               0
"deleted_wake_locks"    36      0       0     0             2593991         518798          1068114         0
"radio-interface"       329     0       0     549499512     437556091307    299558441157    2895507812      31701540695418
"vbus_present"          3       2       0     16399444580   26400295008545  25248585083008  25852771240234  31685690780867
"main"                  27      0       0     547991455078  4674244259033   0               627599700928    31154098800887
"mmc_delayed_work"      796     796     172   0             405246968975    396150246568    689067383       31153556991805
"SMD_DS"                682     681     192   558929443     486005943591    268692961407    2368892822      31701531357039 
```

## Patch submission controversy

Arve Hjønnevå (of Google) sent patches to the linux-pm mailing list in of 2009, but they were rejected. See [this thread](https://lists.linux-foundation.org/pipermail/linux-pm/2009-February/019750.html) for the submission and resulting discussion. This was the third version of the patches submitted for review by the kernel community.

The reasons for the rejection are described in the LWN.NET article [Wakelocks and the embedded problem](http://lwn.net/Articles/318611/).

## Wakelock documentation (from patch)

The 3rd version of the wakelock patch included the following /Documentation/power/wakelock.txt file

```
Wakelocks
=========

A locked wakelock, depending on its type, prevents the system from entering
suspend or other low-power states. When creating a wakelock, you can select
if it prevents suspend or low-power idle states.  If the type is set to
WAKE_LOCK_SUSPEND, the wakelock prevents a full system suspend. If the type
is WAKE_LOCK_IDLE, low-power states that cause large interrupt latencies, or
that disable a set of interrupts, will not be entered from idle until the
wakelocks are released. Unless the type is specified, this document refers
to wakelocks with the type set to WAKE_LOCK_SUSPEND.

If the suspend operation has already started when locking a wakelock, it will
abort the suspend operation as long it has not already reached the suspend_late
stage. This means that locking a wakelock from an interrupt handler or a
freezeable thread always works, but if you lock a wakelock from a suspend_late
handler you must also return an error from that handler to abort suspend.

Wakelocks can be used to allow user-space to decide which keys should wake the
full system up and turn the screen on. Use set_irq_wake or a platform specific
api to make sure the keypad interrupt wakes up the cpu. Once the keypad driver
has resumed, the sequence of events can look like this:
- The Keypad driver gets an interrupt. It then locks the keypad-scan wakelock
  and starts scanning the keypad matrix.
- The keypad-scan code detects a key change and reports it to the input-event
  driver.
- The input-event driver sees the key change, enqueues an event, and locks
  the input-event-queue wakelock.
- The keypad-scan code detects that no keys are held and unlocks the
  keypad-scan wakelock.
- The user-space input-event thread returns from select/poll, locks the
  process-input-events wakelock and then calls read in the input-event device.
- The input-event driver dequeues the key-event and, since the queue is now
  empty, it unlocks the input-event-queue wakelock.
- The user-space input-event thread returns from read. It determines that the
  key should not wake up the full system, releases the process-input-events
  wakelock and calls select or poll.

                 Key pressed   Key released
                     |             |
keypad-scan          ++++++++++++++++++
input-event-queue        +++          +++
process-input-events       +++          +++

Driver API
==========

A driver can use the wakelock api by adding a wakelock variable to its state
and calling wake_lock_init. For instance:
struct state {
    struct wakelock wakelock;
}

init() {
    wake_lock_init(&state->wakelock, WAKE_LOCK_SUSPEND, "wakelockname");
}

Before freeing the memory, wake_lock_destroy must be called:

uninit() {
    wake_lock_destroy(&state->wakelock);
}

When the driver determines that it needs to run (usually in an interrupt
handler) it calls wake_lock:
    wake_lock(&state->wakelock);

When it no longer needs to run it calls wake_unlock:
    wake_unlock(&state->wakelock);

It can also call wake_lock_timeout to release the wakelock after a delay:
    wake_lock_timeout(&state->wakelock, HZ);

This works whether the wakelock is already held or not. It is useful if the
driver woke up other parts of the system that do not use wakelocks but
till need to run. Avoid this when possible, since it will waste power
if the timeout is long or may fail to finish needed work if the timeout is
short. 
```

## Motorola quickwakeup feature

Jocelyn Falempe of Motorola proposed (in November, 2009) a quickwakeup feature to make it possible to reduce the time for a periodic job to resume, do a small amount of work, and suspend again.

[`patchwork.kernel.org/patch/58064/`](http://patchwork.kernel.org/patch/58064/)

From the patch:

```
The purpose of this feature is to drastically reduce the suspend/resume
time for device driver which needs to do periodic job.
In our use case (android smartphone), the system is most of the time in
suspend to RAM, and needs to send a low level command every 30s. With
current framework it takes about 500ms on omap3430 to resume the full
system, and then suspend again. With quickwakup feature, in the resume
process after resuming sysdev and re-enabling irq, the driver handler is
executed, and then it suspends again.
This new path takes 20ms for us, which leads to good power-saving. 
```

## Earlysuspend

Arve's patches also included something referred to as "earlysuspend", but I haven't reviewed this yet to see what it is.

# May 2010 patch submission - "Suspend blockers"

Arve refactored the patches and did a name change, and submitted them again in April and May of 2010\. There was a LOT of discussion on the linux-pm mailing list, which many developers participated in. The discussion raised lots of questions, and lots of responses were given. People interested in Android power management may get more enlightenment by reading the thread.

[`groups.google.com/group/linux.kernel/browse_frm/thread/b6fed7e38365c259/c92d8b4a41f87902?hl=en&tvc=1&q=linux.kernel+suspend+block+api+(version+6)#c92d8b4a41f87902`](http://groups.google.com/group/linux.kernel/browse_frm/thread/b6fed7e38365c259/c92d8b4a41f87902?hl=en&tvc=1&q=linux.kernel+suspend+block+api+(version+6)#c92d8b4a41f87902)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Security

> From: [eLinux.org](http://eLinux.org/Android_Security "http://eLinux.org/Android_Security")

# Android Security

## Contents

*   1 Overview
*   2 Kernel-level security
    *   2.1 Users and groups
        *   2.1.1 Sample File Permissions
        *   2.1.2 Sample Process List
    *   2.2 Paranoid network-ing
*   3 Application-level security
*   4 Tutorial
*   5 Security Analysis
*   6 Security Investigations
    *   6.1 TOMOYO Linux investigation
        *   6.1.1 Presentation
        *   6.1.2 Code
        *   6.1.3 Contact
*   7 Security Tricks
    *   7.1 Changing application security permissions after installation

## Overview

The overall architecture of Android security is described at: [`developer.android.com/guide/topics/security/security.html`](http://developer.android.com/guide/topics/security/security.html)

Each application is given its own Linux user id (UID) and group ID

## Kernel-level security

### Users and groups

Each application is assigned a uid and gid at install time. Application data files are stored in /data/data/\<app-nameu0005c class="calibre16">/..., and are read-writable only by that application process.</app-nameu0005c>

#### Sample File Permissions

Here is an example from my ADP1 phone (lots of lines omitted to reduce noise):

(Oh, and yes, I'm using busybox - find, xargs, and sort are not available otherwise)

```
# find /data/data -type f | xargs ls -l | sort -k3 -n
-rw-------    1 1000     1000         1954 Nov 12 01:10 /data/data/com.android.providers.subscribedfeeds/files/sslcache/android.clients.google.com.443
-rw-r--r--    1 1000     1000       147608 Apr  6  2009 /data/data/com.google.tts/lib/libspeechsynthesis.so
-rw-rw----    1 1000     1000           65 Nov  5 02:01 /data/data/com.google.android.systemupdater/shared_prefs/system_update_helper.xml
-rw-rw----    1 1000     1000          679 Nov 11 23:18 /data/data/com.android.settings/shared_prefs/com.android.settings_preferences.xml
-rw-rw----    1 1000     1000         2000 May 14 20:07 /data/data/com.google.android.location/files/DATA_Preferences
-rw-rw----    1 1000     1000         6144 Dec 19  2008 /data/data/com.android.settings/databases/webviewCache.db
-rw-rw----    1 1000     1000        11264 Nov 12 01:10 /data/data/com.android.providers.subscribedfeeds/databases/subscribedfeeds.db
-rw-rw----    1 1000     1000        14336 Dec 19  2008 /data/data/com.android.settings/databases/webview.db
-rw-rw----    1 1000     1000        36864 Nov 12 18:23 /data/data/com.android.providers.settings/databases/settings.db
-rw-rw----    1 1000     1000       129024 Nov 12 18:45 /data/data/com.google.android.server.checkin/databases/checkin.db
-rw-rw-r--    1 1000     1000          120 Nov 12 01:09 /data/data/com.android.providers.subscribedfeeds/shared_prefs/subscribedFeeds.xml
-rwxrwx---    1 1000     1000        54052 Dec 20  2008 /data/data/com.android.settings/files/wallpaper
-rw-------    1 1001     1001            4 Oct 31 21:09 /data/data/com.android.providers.telephony/app_parts/PART_1257023388570
-rw-------    1 1001     1001            4 Oct 31 21:10 /data/data/com.android.providers.telephony/app_parts/PART_1257023445796

...
-rw-rw----    1 1001     1001          103 May 13  2009 /data/data/com.android.providers.telephony/shared_prefs/preferred-apn.xml
-rw-rw----    1 1001     1001          122 Oct 28 17:37 /data/data/com.android.phone/shared_prefs/com.android.phone_preferences.xml
-rw-rw----    1 1001     1001          126 Sep  3  2008 /data/data/com.android.phone/shared_prefs/_has_set_default_values.xml
-rw-rw----    1 1001     1001         7168 Nov  5 02:01 /data/data/com.android.providers.telephony/databases/telephony.db
-rw-rw----    1 1001     1001        69632 Nov  6 01:58 /data/data/com.android.providers.telephony/databases/mmssms.db
-rw-rw----    1 10000    10000         114 Apr 20  2009 /data/data/com.android.alarmclock/shared_prefs/AlarmClock.xml
-rw-rw----    1 10000    10000        4096 Dec 19  2008 /data/data/com.android.alarmclock/databases/alarms.db
-rw-rw----    1 10001    10001        7168 Nov 12 18:43 /data/data/org.koxx.forecast_weather.v2/databases/forecasts.db
-rw-rw----    1 10002    10002         489 Nov 11 23:19 /data/data/com.android.calculator2/files/calculator.data
-rw-rw----    1 10003    10003         683 Jun 10 19:27 /data/data/com.android.camera/shared_prefs/com.android.camera_preferences.xml
-rw-rw----    1 10003    10003        5120 Dec 20  2008 /data/data/com.android.providers.drm/databases/drm.db
-rw-rw----    1 10003    10003       10240 Nov  1 16:24 /data/data/com.android.providers.downloads/databases/downloads.db
-rw-rw----    1 10003    10003       37888 May 13  2009 /data/data/com.android.providers.media/databases/internal.db
-rw-rw----    1 10003    10003       37888 Sep  4 23:25 /data/data/com.android.camera/databases/launcher.db
-rw-rw----    1 10003    10003       60416 Nov 12 19:01 /data/data/com.android.providers.media/databases/external-39636438.db
-rw-r--r--    1 10004    10004           0 Jun 12 01:13 /data/data/com.android.providers.im/databases/im.db-mj76B91FF8
-rw-r--r--    1 10004    10004           0 Jun 12 04:05 /data/data/com.android.providers.im/databases/im.db-mj0AB1E39C
...
-rw-rw----    1 10004    10004         105 Dec 18  2008 /data/data/com.android.providers.contacts/shared_prefs/owner-info.xml
-rw-rw----    1 10004    10004         125 Nov 11 16:37 /data/data/com.android.contacts/shared_prefs/dialtacts.xml
-rw-rw----    1 10004    10004         126 Dec 19  2008 /data/data/com.android.contacts/shared_prefs/_has_set_default_values.xml
-rw-rw----    1 10004    10004         146 Aug 28 16:02 /data/data/com.android.contacts/shared_prefs/com.android.contacts_preferences.xml
-rw-rw----    1 10004    10004         169 Nov  5 02:01 /data/data/com.android.launcher/shared_prefs/launcher.xml
-rw-rw----    1 10004    10004        4096 Jan 30  2009 /data/data/com.android.providers.userdictionary/databases/user_dict.db
-rw-rw----    1 10004    10004       20480 Oct 31 21:12 /data/data/com.android.launcher/databases/launcher.db
-rw-rw----    1 10004    10004       21504 Nov 12 18:45 /data/data/com.android.providers.im/databases/im.db
-rw-rw----    1 10004    10004      110592 Nov 12 02:08 /data/data/com.android.providers.contacts/databases/contacts.db
-rw-------    1 10005    10005         270 Jun 13 03:36 /data/data/com.android.email/databases/0c180cf8-fb7b-4d3e-b994-4282611af63a.db_att/32
-rw-r--r--    1 10005    10005     1418240 Nov  5 02:01 /data/data/com.android.email/databases/0c180cf8-fb7b-4d3e-b994-4282611af63a.db
-rw-rw----    1 10005    10005        1866 Dec 20  2008 /data/data/com.android.email/shared_prefs/AndroidMail.Main.xml
-rw-rw----    1 10005    10005        6144 Sep  8 01:35 /data/data/com.android.email/databases/webviewCache.db
-rw-rw----    1 10005    10005       14336 May 14 17:58 /data/data/com.android.email/databases/webview.db
-rw-rw----    1 10006    10006         126 Dec 18  2008 /data/data/com.google.android.gm/shared_prefs/_has_set_default_values.xml
-rw-rw----    1 10006    10006         199 Jan 22  2009 /data/data/com.google.android.gm/shared_prefs/Gmail.xml
-rw-rw----    1 10006    10006        6144 Dec 19  2008 /data/data/com.google.android.gm/databases/gmail.db
-rw-rw----    1 10006    10006        6144 Dec 23  2008 /data/data/com.google.android.gm/databases/webviewCache.db
-rw-rw----    1 10006    10006       14336 Dec 23  2008 /data/data/com.google.android.gm/databases/webview.db
-rw-------    1 10007    10007        1888 Nov 12 17:09 /data/data/com.google.android.apps.gtalkservice/files/sslcache/mtalk.google.com.5228
-rw-------    1 10007    10007        1954 Nov 12 18:43 /data/data/com.google.android.providers.gmail/files/sslcache/android.clients.google.com.443
-rw-rw----    1 10007    10007        6144 Oct 23 22:43 /data/data/com.google.android.googleapps/databases/webviewCache.db
-rw-rw----    1 10007    10007        7168 May 13  2009 /data/data/com.google.android.providers.settings/databases/googlesettings.db
-rw-rw----    1 10007    10007       13312 Nov 11 20:37 /data/data/com.google.android.googleapps/databases/accounts.db
-rw-rw----    1 10007    10007       14336 May 13  2009 /data/data/com.google.android.googleapps/databases/webview.db
-rw-rw----    1 10007    10007      502784 Nov 12 18:45 /data/data/com.google.android.providers.gmail/databases/mailstore.tbird20d@gmail.com.db
-rw-rw----    1 10009    10009         126 Sep  3  2008 /data/data/com.android.mms/shared_prefs/_has_set_default_values.xml
-rw-rw----    1 10009    10009         585 Sep  3  2008 /data/data/com.android.mms/shared_prefs/com.android.mms_preferences.xml
-rw-rw-rw-    1 10010    10010         310 Sep 18 01:12 /data/data/com.android.music/shared_prefs/Music.xml
-rw-rw----    1 10015    10015         126 Apr 29  2009 /data/data/com.google.android.street/shared_prefs/com.google.android.street.StreetView.xml
-rw-------    1 10017    10017          35 Nov 12 16:49 /data/data/com.android.browser/cache/webviewCache/c24b0576
-rw-------    1 10017    10017          43 Nov 12 16:47 /data/data/com.android.browser/cache/webviewCache/5446c8f2
...
-rw-------    1 10017    10017     1204872 May 13  2009 /data/data/com.android.browser/app_plugins/gears.so
-rw-r--r--    1 10017    10017         512 Nov 12 19:18 /data/data/com.android.browser/databases/webviewCache.db-journal
-rw-r--r--    1 10017    10017        8192 May 14 19:15 /data/data/com.android.browser/gears/geolocation.db
-rw-r--r--    1 10017    10017       18432 Dec 19  2008 /data/data/com.android.browser/gears/localserver.db
-rw-r--r--    1 10017    10017       20480 Dec 19  2008 /data/data/com.android.browser/gears/permissions.db
-rw-r--r--    1 10017    10017       48128 Nov 12 19:01 /data/data/com.android.browser/app_icons/WebpageIcons.db
-rw-rw----    1 10017    10017         851 May 29 13:53 /data/data/com.android.browser/shared_prefs/com.android.browser_preferences.xml
-rw-rw----    1 10017    10017       32768 Nov 12 16:49 /data/data/com.android.browser/databases/webviewCache.db
-rw-rw----    1 10017    10017       68608 Nov 12 16:49 /data/data/com.android.browser/databases/browser.db
-rw-rw----    1 10017    10017      257024 Nov 12 17:09 /data/data/com.android.browser/databases/webview.db
-rw-rw-rw-    1 10017    10017           0 Nov 12 16:48 /data/data/com.android.browser/app_plugins/gears-0.5.17.0/gearstimestamp
-rw-rw----    1 10018    10018         126 Sep  3  2008 /data/data/com.android.calendar/shared_prefs/_has_set_default_values.xml
-rw-rw----    1 10018    10018         539 Nov 11 23:19 /data/data/com.android.calendar/shared_prefs/com.android.calendar_preferences.xml
-rw-rw----    1 10018    10018      375808 Nov 12 09:58 /data/data/com.android.providers.calendar/databases/calendar.db
-rw-rw----    1 10019    10019          48 Nov  7 06:11 /data/data/com.google.android.apps.maps/files/DATA_Tiles
-rw-rw----    1 10019    10019         483 Nov 11 03:58 /data/data/com.google.android.apps.maps/shared_prefs/com.google.android.maps.MapsActivity.xml
-rw-rw----    1 10019    10019         708 Nov 12 17:09 /data/data/com.google.android.apps.maps/shared_prefs/friend_finder.xml
-rw-rw----    1 10019    10019        2000 Nov 11 03:58 /data/data/com.google.android.apps.maps/files/DATA_Preferences
-rw-rw----    1 10019    10019        6144 May 13  2009 /data/data/com.google.android.apps.maps/databases/webviewCache.db
-rw-rw----    1 10019    10019        6144 Nov  1 21:28 /data/data/com.google.android.apps.maps/databases/search_history.db
-rw-rw----    1 10019    10019        8192 Nov 11 03:52 /data/data/com.google.android.apps.maps/databases/friends.db
-rw-rw----    1 10019    10019       14336 May 13  2009 /data/data/com.google.android.apps.maps/databases/webview.db
-rw-rw----    1 10019    10019       16048 Nov  7 06:11 /data/data/com.google.android.apps.maps/files/DATA_Tiles_1
-rw-rw-rw-    1 10019    10019          65 Nov 11 03:52 /data/data/com.google.android.apps.maps/shared_prefs/extra-features.xml
-rw-rw----    1 10021    10021         435 Oct 30 17:09 /data/data/com.android.vending/shared_prefs/vending_preferences.xml
-rw-rw----    1 10021    10021        5120 Oct  6 19:38 /data/data/com.android.vending/databases/suggestions.db
-rw-rw----    1 10021    10021        6144 May 14 17:17 /data/data/com.android.vending/databases/webviewCache.db
-rw-rw----    1 10021    10021       14336 May 14 17:21 /data/data/com.android.vending/databases/webview.db
-rw-rw----    1 10021    10021       17408 Oct  6 19:39 /data/data/com.android.vending/databases/assets.db
-rw-------    1 10022    10022       50077 Nov 12 05:34 /data/data/com.google.android.youtube/cache/videos?vq=peter+sellers+inspector&format=2&restriction=us&start-index=18&max-results=8
-rw-------    1 10022    10022       53110 Nov 12 05:33 /data/data/com.google.android.youtube/cache/videos?vq=peter+sellers+inspector&format=2&restriction=us&start-index=10&max-results=8
-rw-------    1 10022    10022       57403 Nov 12 05:33 /data/data/com.google.android.youtube/cache/videos?vq=peter+sellers+inspector&format=2&restriction=us&start-index=1&max-results=9
-rw-------    1 10022    10022       63761 Nov 12 05:32 /data/data/com.google.android.youtube/cache/recently_featured?format=2&start-index=1&max-results=9
-rw-rw----    1 10022    10022         739 Nov 12 16:45 /data/data/com.google.android.youtube/shared_prefs/youtube.xml
-rw-rw----    1 10022    10022        5120 Nov 12 05:34 /data/data/com.google.android.youtube/databases/suggestions.db
-rw-rw----    1 10025    10025         114 May 13  2009 /data/data/com.google.android.voicesearch/shared_prefs/com.google.android.voicesearch_preferences.xml
-rw-rw----    1 10025    10025         126 May 13  2009 /data/data/com.google.android.voicesearch/shared_prefs/_has_set_default_values.xml
-rw-rw----    1 10025    10025        2000 May 13  2009 /data/data/com.google.android.voicesearch/files/DATA_Preferences
-rw-rw----    1 10025    10025        8192 Jun 10 03:25 /data/data/com.google.android.voicesearch/databases/webviewCache.db
-rw-rw----    1 10025    10025       14336 May 13  2009 /data/data/com.google.android.voicesearch/databases/webview.db
-rw-rw----    1 10026    10026         688 Jan 10  2009 /data/data/com.quirkconsulting/shared_prefs/TouchTipv2.xml
-rw-rw----    1 10026    10026        6144 Dec 19  2008 /data/data/com.quirkconsulting/databases/webviewCache.db
-rw-rw----    1 10026    10026       14336 Dec 19  2008 /data/data/com.quirkconsulting/databases/webview.db
-rw-------    1 10027    10027        4326 Aug  8 01:03 /data/data/com.a0soft.gphone.aCurrency/app_db/currency.db
-rw-rw----    1 10027    10027         170 Aug  8 01:04 /data/data/com.a0soft.gphone.aCurrency/shared_prefs/com.a0soft.gphone.aCurrency_preferences.xml
-rw-rw----    1 10027    10027         740 Dec 20  2008 /data/data/com.a0soft.gphone.aCurrency/shared_prefs/pref2.xml
-rw-rw----    1 10027    10027         801 Aug  8 01:05 /data/data/com.a0soft.gphone.aCurrency/shared_prefs/pref3.xml
-rw-rw----    1 10027    10027        6144 Aug  8 01:03 /data/data/com.a0soft.gphone.aCurrency/databases/webviewCache.db
-rw-rw----    1 10027    10027       14336 Aug  8 01:03 /data/data/com.a0soft.gphone.aCurrency/databases/webview.db
-rw-rw----    1 10028    10028       14336 Sep 19 22:17 /data/data/com.stylem.movies/databases/webview.db
-rw-rw----    1 10028    10028       53248 Sep 19 22:18 /data/data/com.stylem.movies/databases/webviewCache.db
-rw-rw----    1 10029    10029         241 Jan 10  2009 /data/data/com.capaci.android.flashlight/shared_prefs/SettingsFile.xml
-rw-rw----    1 10030    10030         233 Jun 15 15:02 /data/data/com.weather.Weather/files/tile-Radar-023010230-200906151450-twc.png
-rw-rw----    1 10030    10030         233 Jun 15 15:02 /data/data/com.weather.Weather/files/tile-Radar-023010231-200906151450-twc.png
-rw-rw----    1 10030    10030         233 Jun 15 15:02 /data/data/com.weather.Weather/files/tile-Radar-023010232-200906151450-twc.png
-rw-rw----    1 10030    10030         233 Mar 18  2009 /data/data/com.weather.Weather/files/tile-Radar-023010221-200903181940-twc.png
-rw-rw----    1 10030    10030         233 Mar 18  2009 /data/data/com.weather.Weather/files/tile-Radar-023010223-200903181940-twc.png
-rw-rw----    1 10030    10030         233 Mar 18  2009 /data/data/com.weather.Weather/files/tile-Radar-023010230-200903181940-twc.png
-rw-rw----    1 10030    10030         233 Mar 18  2009 /data/data/com.weather.Weather/files/tile-Radar-023010232-200903181940-twc.png 
```

When Dalvik (actually, the 'zygote' process') loads an application, it changes to the uid and gid for the application, so that the process is running in the correct security context.

#### Sample Process List

If you compare the Uids below with the above list, you'll see the correspondence between the assigned UID and the running processes. (For example, 10017 is the browser).

```
# ps
  PID  Uid        VSZ Stat Command
    1 0           288 S   /init
[kernel threads omitted...]
   30 1000        808 S   /system/bin/servicemanager
   31 0           848 S   /system/bin/vold
   32 0           668 S   /system/bin/debuggerd
   33 1001       7888 S   /system/bin/rild
   34 0         70548 S   zygote /bin/app_process -Xzygote /system/bin --zygote
   35 1013      30032 S   /system/bin/mediaserver
   36 1002       1172 S   /system/bin/dbus-daemon --system --nofork
   37 0           816 S   /system/bin/installd
   39 0           744 S   /system/bin/sh /runme.sh
   40 1008       1304 S   /system/bin/akmd
   41 0          3340 S   /sbin/adbd
   64 1000     171284 S   system_server
  108 1001     122172 S   com.android.phone
  110 10004    129936 S   android.process.acore
  387 10004    101668 S   com.android.inputmethod.latin
 6721 10017    158272 S   com.android.browser
 6901 10019     96340 S   com.google.android.apps.maps
 7166 0           740 S   /system/bin/sh -
 7635 10007    123776 S   com.google.process.gapps
 7727 10000     91284 S   com.android.alarmclock
 7753 0           872 S   sleep 3
 7754 0          2104 R   ps 
```

### Paranoid network-ing

Android adds a "paranoid network" option to the Linux kernel, which restricts access to some networking features depending on the group of the calling process.

The list of groups that are allowed access to networking features is in the kernel source file: */include/linux/android_aids.h*

Here is the list:

| #define | GID | Capability |
| AID_NET_BT_ADMIN | 3001 | Can create an RFCOMM, SCO, or L2CAPP Bluetooth socket |
| AID_NET_BT | 3002 | Can create a Bluetooth socket |
| AID_INET | 3003 | Can create IPv4 or IPv6 socket |
| AID_NET_RAW | 3004 | Can create certain kinds of IPv4 sockets?? |
| AID_NET_ADMIN* | 3005 | Allow CAP_NET_ADMIN permissions for process |

Note: * Added in Donut (not in original Android 1.0)

## Application-level security

Android also uses a user-space level security system to regulate communication and interaction among applications and system components. This is described at: [`developer.android.com/guide/topics/security/security.html`](http://developer.android.com/guide/topics/security/security.html)

## Tutorial

See [`siis.cse.psu.edu/android-tutorial.html`](http://siis.cse.psu.edu/android-tutorial.html)

(With an abridged version at: [`siis.cse.psu.edu/android_sec_tutorial.html`](http://siis.cse.psu.edu/android_sec_tutorial.html))

## Security Analysis

See a good analysis of Android security at: [`www.isecpartners.com/files/iSEC_Android_Exploratory_Blackhat_2009.pdf`](http://www.isecpartners.com/files/iSEC_Android_Exploratory_Blackhat_2009.pdf)

## Security Investigations

### [TOMOYO Linux](http://elinux.org/TomoyoLinux) investigation

#### Presentation

*   ["Learning, Analyzing and Protecting Android with TOMOYO Linux (JLS2009)"](http://www.slideshare.net/haradats/learning-analyzing-and-protecting-android-with-tomoyo-linux-jls2009) (Japan Linux Symposium 2009, Oct. 2009)
*   ["TOMOYO Linux on Android"](http://www.slideshare.net/haradats/taipei2009) ([Smartbook/Netbook/Mobile Application Conference Taipei 2009](http://www.slideshare.net/haradats/taipei2009), Oct. 2009)
*   ["TOMOYO Linux on Android"](http://www.slideshare.net/haradats/tomoyo-linux-on-android) (CELF Japan Technical Jamboree 28, Jun. 2009)

*   [other English slides on slideshare of TOMOYO Linux](http://www.slideshare.net/haradats/presentations?order=popular)

*   [all English slides PDFs of TOMOYO Linux](http://sourceforge.jp/projects/tomoyo/docs/?category_id=532&language_id=1)

#### Code

*   [tomoyo-android-jp - Project Hosting on Google Code](http://code.google.com/p/tomoyo-android-jp/)

#### Contact

haradats@gmail.com

## Security Tricks

### Changing application security permissions after installation

Some applications request more permissions than they really need. You can alter the set of permissions granted to an application by editing /data/system/pacakges.xml.

lbcoder wrote this on the android-platform mailing list:

```
Go into /data/system/packages.xml and you can remove permission lines.
Immediately after saving the packages.xml, reboot the phone (otherwise
the file will get overwritten by the system again). The newly reduced
permissions will be read on boot. 
```

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Memory Usage

> From: [eLinux.org](http://eLinux.org/Android_Memory_Usage "http://eLinux.org/Android_Memory_Usage")

# Android Memory Usage

The memory of an Android system is managed by several different allocators, in several different pools.

## Contents

*   1 System Memory
*   2 Process Memory
    *   2.1 procrank
    *   2.2 smem tool
*   3 Dalvik Heap
*   4 Debugging Android application memory usage
    *   4.1 How to debug native process memory allocations
    *   4.2 libc.debug.malloc
*   5 References

## System Memory

You can examine the system's view of the memory on the machine, by examining /proc/meminfo.

If you use 'ddms', you can see a summary of the memory used on the machine, by the system and by the different executing processes. Click on the SysInfo tab, and select "Memory Usage" in the box on the upper left of the pane.

Here's a screenshot:

![Android memory usage on an OMAP EVM platform, running eclair, as
shown by
ddms](http://eLinux.org/File:Ddms-memory-usage1.png "Android memory usage on an OMAP EVM platform, running eclair, as shown by ddms")

Note that you can get the numbers for each process by hovering your mouse over a particular pie slice. Numbers are shown in K and percentages.

## Process Memory

You can see an individual process' memory usage by examining /proc/\<pidu0005c class="calibre16">/status</pidu0005c>

Details about memory usage are in

*   /proc/\<pidu0005c class="calibre16">/statm</pidu0005c>
*   /proc/\<pidu0005c class="calibre16">/maps</pidu0005c>
*   /proc/\<pidu0005c class="calibre16">/smaps</pidu0005c>

The 'top' command will show VSS and RSS.

Also, see ddms info above.

### procrank

procrank will show you a quick summary of process memory utilization. By default, it shows Vss, Rss, Pss and Uss, and sorts by Vss. However, you can control the sorting order.

procrank source is included in system/extras/procrank, and the binary is located in /system/xbin on an android device.

*   Vss = virtual set size
*   Rss = resident set size
*   Pss = proportional set size
*   Uss = unique set size

In general, the two numbers you want to watch are the Pss and Uss (Vss and Rss are generally worthless, because they don't accurately reflect a process's usage of pages shared with other processes.)

*   Uss is the set of pages that are unique to a process. This is the amount of memory that would be freed if the application was terminated right now.
*   Pss is the amount of memory shared with other processes, accounted in a way that the amount is divided evenly between the processes that share it. This is memory that would not be released if the process was terminated, but is indicative of the amount that this process is "contributing"

to the overall memory load.

You can also use procrank to view the working set size of each process, and to reset the working set size counters.

Here is procrank's usage:

```
# procrank -h
Usage: procrank [ -W ] [ -v | -r | -p | -u | -h ]
    -v  Sort by VSS.
    -r  Sort by RSS.
    -p  Sort by PSS.
    -u  Sort by USS.
        (Default sort order is PSS.)
    -R  Reverse sort order (default is descending).
    -w  Display statistics for working set only.
    -W  Reset working set of all processes.
    -h  Display this help screen. 
```

And here is some sample output:

```
# procrank
  PID      Vss      Rss      Pss      Uss  cmdline
 1217   36848K   35648K   17983K   13956K  system_server
 1276   32200K   32200K   14048K   10116K  android.process.acore
 1189   26920K   26920K    9293K    5500K  zygote
 1321   20328K   20328K    4743K    2344K  android.process.media
 1356   20360K   20360K    4621K    2148K  com.android.email
 1303   20184K   20184K    4381K    1724K  com.android.settings
 1271   19888K   19888K    4297K    1764K  com.android.inputmethod.latin
 1332   19560K   19560K    3993K    1620K  com.android.alarmclock
 1187    5068K    5068K    2119K    1476K  /system/bin/mediaserver
 1384     436K     436K     248K     236K  procrank
    1     212K     212K     200K     200K  /init
  753     572K     572K     171K     136K  /system/bin/rild
  748     340K     340K     163K     152K  /system/bin/sh
  751     388K     388K     156K     140K  /system/bin/vold
 1215     148K     148K     136K     136K  /sbin/adbd
  757     352K     352K     117K      92K  /system/bin/dbus-daemon
  760     404K     404K     104K      80K  /system/bin/keystore
  759     312K     312K     102K      88K  /system/bin/installd
  749     288K     288K      96K      84K  /system/bin/servicemanager
  752     244K     244K      71K      60K  /system/bin/debuggerd 
```

In this example, it shows that the native daemons and programs are an order of magnitude smaller than the Dalvik-based services and programs. Also, even the smallest Dalvik program requires about 1.5 meg (Uss) to run.

### smem tool

You can see very detailed per-process or systemwide memory information with smem.

See [Using smem on Android](http://eLinux.org/Using_smem_on_Android "Using smem on Android")

## Dalvik Heap

The Dalvik heap is preloaded with classes and data by zygote (loading over 1900 classes as of Android version 2.2). When zygote forks to start an android application, the new application gets a copy-on-write mapping of this heap. As Dan Borstein says below, this helps with memory reduction as well as application startup time.

Dalvik, like virtual machines for many other languages, does garbage collection on the heap. There appears to be a separate thread (called the HeapWorker) in each VM process that performs the garbage collection actions. (See toolbox ps -t) [need more notes on the garbage collection]

Dan Borstein said this about heap sharing^([[1]](#cite_note-1)):

It's used in Android to amortize the RAM footprint of the large amount of effectively-read-only data (technically writable but rarely actually written) associated with common library classes across all active VM processes. 1000+ classes get preloaded by the system at boot time, and each class consumes at least a little heap for itself, including often pointing off to a constellation of other objects. The heap created by the preloading process gets shared copy-on-write with each spawned VM process (but again doesn't in practice get written much). This saves hundreds of kB of dirty unpageable RAM per process and also helps speed up process startup.

[INFO NEEDED: how to show dalvik heap info?]

## Debugging Android application memory usage

See an excellent article by Dianne Hackborn at: [`stackoverflow.com/questions/2298208/how-to-discover-memory-usage-of-my-application-in-android/2299813#2299813`](http://stackoverflow.com/questions/2298208/how-to-discover-memory-usage-of-my-application-in-android/2299813#2299813)

### How to debug native process memory allocations

```
setprop dalvik.vm.checkjni true
setprop libc.debug.malloc 10
setprop setprop dalvik.vm.jniopts forcecopy
start
stop 
```

### libc.debug.malloc

The C library (bionic) in the system supports the ability to utilize a different, debug, version of the malloc code at runtime in the system.

If the system property libc.debug.malloc has a value other than 0, then when a process is instantiated, the C library uses functions for allocating and freeing memory, for that process.

(Note that there are other ways that the debug shared library malloc code ends up being used as well. That is, if you are running in the emulator, and the value of the system property ro.kernel.memcheck is not '0', then you get a debug level of 20\. Note that debug level 20 can only be used in the emulator.)

By default, the standard malloc/free/calloc/realloc/memalign routines are used. By setting libc.debug.malloc, different routines are used, which check for certain kinds of memory errors (such as leaks and overruns). This is done by loading a separate shared library (.so) with these different routines.

The shared libraries are named: /system/lib/libc_malloc_debug_leak.so and /system/lib/libc_malloc_debug_qemu.so

(Information was obtained by looking at \<android-source-rootu0005c class="calibre16">/bionic/libc/bionic/malloc_debug_common.c)</android-source-rootu0005c>

Supported values for libc.debug.malloc (debug level values) are:

*   1 - perform leak detection
*   5 - fill allocated memory to detect overruns
*   10 - fill memory and add sentinels to detect overruns
*   20 - use special instrumented malloc/free routines for the emulator

I'm not sure whether these shared libraries are shipped in production devices.

## References

1.  ↑ comment by Dan Borstein, Jan 2009 to blog article [Dalvik vs. Mono](http://www.koushikdutta.com/2009/01/dalvik-vs-mono.html)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Dalvik Virtual Machine

> From: [eLinux.org](http://eLinux.org/Android_Dalvik_VM "http://eLinux.org/Android_Dalvik_VM")

# Android Dalvik VM

Dalvik is the name of the Virtual Machine in which Android applications are run. This VM executes Dalvik bytecode, which is compiled from programs written in the Java language. Note that the Dalvik VM is not a Java VM (JVM).

Every Android application runs in its own process, with its own instance of the Dalvik virtual machine.

At boot time, a single virtual machine, called 'zygote' is created, which preloads a long list of classes. (As of Android version 2.1 (eclair), the list of classes preloaded by zygote had 1,942 entries). All other "java" programs or services are forked from this process, and run as their own process (and threads) in their own address space. Both applications and system services in the Android framework are implemented in "java".

Dalvik was written so that a device can run multiple VMs efficiently. The Dalvik VM executes code in the Dalvik Executable (.dex) format which is optimized for minimal memory footprint. The VM is register-based, and runs classes compiled by a Java language compiler that have been transformed into the .dex format by the included "dx" tool.

Most Android applications are delivered and stored on the system as packages (.apks), which include both dex bytes code (classes and methods) and resources. During first boot-up the system creates a cache of dex classes in /data/dalvik-cache.

## Contents

*   1 Documentation
*   2 Q&A
*   3 JIT
*   4 Relationship to Java
*   5 Dalvik on other platforms
*   6 Debugging the VM
    *   6.1 Getting stack traces
    *   6.2 Using checkJNI
*   7 Resources

## Documentation

There is some documentation on Dalvik in the source code in the dalvik/docs directory. See the [Android dalvik docs git repository](http://android.git.kernel.org/?p=platform/dalvik.git;a=tree;f=docs;).

The source code has some rather large comments, including near the top of Thread.c and Exception.c. The "mterp" directory has some notes describing the structure of the interpreters.

## Q&A

From fadden on the android-platform list:

*   Who is responsible to read the dex file and call Dalvik ?

    *   The VM is started by the framework, through the JNI invocation interface. See AndroidRuntime.cpp in frameworks/base/...
*   Do you have a tutorial launch an app in the terminal adb: bytecode file > dex file > app launch in Dalvik => and see the result in adb ?

    *   See "hello-world.html" in the dalvik/docs directory (linked above).
*   Is there a profiler inside Dalvik, which enable us to follow each step in the dalvik execution ?

    *   You could turn on LOG_INSTR to see each instruction as it is executed. This results in a rather dramatic amount of logging. If you try to do this on a device you will overrun the 64KB kernel log buffer pretty quickly and drop lots of stuff, so it's really only suitable for a "desktop" build (e.g. sim-eng).
    *   Also, Dalvik does include instrumentation to allow for tracing and profiling. See [`elinux.org/Android_Tools#traceview`](http://elinux.org/Android_Tools#traceview)

## JIT

As of version 2.2 (*Froyo*), Dalvik includes a Just-In-Time compiler (or JIT).

*   See [A JIT Compiler for Android's Dalvik VM](http://code.google.com/events/io/2010/sessions/jit-compiler-androids-dalvik-vm.html)
    *   video of presentation by Ben Cheng and Bill Buzbee at Google IO, 2010
    *   [Slides, in PDF](http://dl.google.com/googleio/2010/android-jit-compiler-androids-dalvik-vm.pdf)

The Dalvik JIT, as of version 2.2, is a "trace-granularity JIT", which means that it compiles individual code fragments that it discovers at runtime to be "hot spots". (That is, it does not compile whole methods.) The Dalvik bytecode interpreter is constantly profiling the code it is executing, and when a piece of code is determined to be running a lot, it is passed to a compiler to turn into native code. Several optimizations may be performed in this process. This code is then executed instead of the bytecode, for future runs through this section of the software.

The memory overhead of the JIT is reported to be between 100K to 200K per application. The ratio of code size between native instructions and DEX byte codes in one example give (see slide 22 of the presentation) was 7.7 to 1\. That is, native instructions take approximately 8 times as much space as DEX byte codes do to perform the same operations.

## Relationship to Java

Because Dalvik is not referred to as a Java Virtual Machine it does not utilize the branding of "Java". Also, it does not execute Java bytecodes. Hence, Google can ignore licensing issues with Sun or Oracle, with regards to Java.

However, a Java compiler and set of class libraries are required in order to create a Dalvik program.

As of March 2010, only the Sun JDK, version 1.5 is supported for building the Android system and add-on Android applications.

## Dalvik on other platforms

*   Myriad Alien Dalvik - an implementation of the Dalvik VM for other platforms (demonstrated first on Meego)
    *   [`www.linuxfordevices.com/c/a/News/Myriad-Group-Myriad-Alien-Dalvik/?kc=LNXDEVNL020911`](http://www.linuxfordevices.com/c/a/News/Myriad-Group-Myriad-Alien-Dalvik/?kc=LNXDEVNL020911)

## Debugging the VM

There are a number of properties you can set, to control operation of the VM and allow for debugging various aspects of the system:

See [`netmite.com/android/mydroid/dalvik/docs/embedded-vm-control.html`](http://netmite.com/android/mydroid/dalvik/docs/embedded-vm-control.html)

(Note that this is in \<aosp-rootu0005c class="calibre16">/dalvik/docs, along with a whole bunch of other files with information about Dalvik.)</aosp-rootu0005c>

this mentions a number of features you can control with properties, including:

*   checkjni - various checks when JNI is used
*   enableassertions - enable assertions in the VM code
*   verify-bytecode - whether to perform bytecode verification
*   execution-mode - whether to use optimized assembly or portable C code for the interpreter
*   stack-trace-file - where to put stack trace data when a SIGQUIT is received

### Getting stack traces

You can force the VM to dump a stack trace of all threads by sending a SIGQUIT signal. This can be done using 'kill -3 \<pidu0005c class="calibre16">', where pid in the main dalvik process. By default, the stack trace goes to the android log, but you can have the data sent to a file (using the dalvik.vm.stack-trace-file property) instead.</pidu0005c>

### Using checkJNI

CheckJNI refers to a special runtime mode of the Dalvik VM, which forces the VM to run certain check and report problems when it sees certain errors occuring from code called via JNI.

See [`android-developers.blogspot.com/2011/07/debugging-android-jni-with-checkjni.html`](http://android-developers.blogspot.com/2011/07/debugging-android-jni-with-checkjni.html)

## Resources

*   [Dalvik wikipedia entry](http://en.wikipedia.org/wiki/Dalvik_(software))

*   [Dalvik VM Internals](http://sites.google.com/site/io/dalvik-vm-internals) - video of presentation by Dan Bornstein at Google IO, 2008

*   [DEX file format](http://www.retrodev.com/android/dexformat.html), reverse engineered by Michael Pavone

*   [Dalvik bytecodes](http://developer.android.com/reference/dalvik/bytecode/Opcodes.html)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Packages, Assets and Resources

> From: [eLinux.org](http://eLinux.org/Android_Packages "http://eLinux.org/Android_Packages")

# Android Packages

## Contents

*   1 Packages
    *   1.1 AndroidManifest.xml
    *   1.2 Tools for managing packages
*   2 Resources
*   3 Assets

## Packages

Android applications are shipped as "packages", which are compressed archives with class files, resources, and meta-information (the AndroidManifest.xml file and security certificates) for the application.

A package has the extension .apk, and it consists of a set of directories and files in a gzip'ed archive.

A package usually contains the following items:

META-INF/MANIFEST.MF The manifest file for the package file (apk) itself.

META-INF/CERT.SF A security certificate

META-INF/CERT.RSA another security certificate (should distinguish these two)

AndroidManifest.xml The manifest file for the application(s) in this package

classes.dex The actual code for the dalvik ([`eLinux.org/java`](http://eLinux.org/java)) classes for the application(s) in this package

res/ a directory containing resource files

resources.arsc ???

### AndroidManifest.xml

The AndroidManifest.xml file has information about the application(s) in a package. Usually, a package will contain a single application. The AndroidManifest file describes the application's name, as well as libraries and security permissions neede by the app, messages used by the app, what icon to use to represent the app, and more.

See [`developer.android.com/guide/topics/manifest/manifest-intro.html`](http://developer.android.com/guide/topics/manifest/manifest-intro.html) for details.

### Tools for managing packages

The [aapt](http://eLinux.org/Android_aapt "Android aapt") tool is used to create, inspect and modify Android packages.

## Resources

An overview of application resources is at: [`developer.android.com/guide/topics/resources/index.html`](http://developer.android.com/guide/topics/resources/index.html)

It is possible to use a raw file as a resource (without it getting compiled by the build system). See this article on [using raw files as resources](http://thedevelopersinfo.com/2009/11/27/using-files-as-raw-resources-in-android/) in Android.

## Assets

Assets are like resources, except that do not have a resource ID, and they are listed in the 'assets' directory of a package, rather than the 'res' directory.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Networking

> From: [eLinux.org](http://eLinux.org/Android_Networking "http://eLinux.org/Android_Networking")

# Android Networking

Here are some tips for getting networking working on your Android device.

## Setting the DNS server

In my experience, some times the device fails to get it's DNS server correctly from DHCP. The DNS server address is retrieved from system properties.

You can set this manually by typing (on the target):

```
$ setprop net.eth0.dns1 xx.yy.zz.aa 
```

(replacing with the correct numeric values for your DNS server address)

I have also set the following:

```
$ setprop net.nds1 xx.yy.zz.aa 
```

This needs to be set each time the system boots. I'm sure there's a way to have 'init' set this, or to put it into persistent properties, but I haven't done that yet.

## Configuring a web proxy

The browser looks at system settings stored in a provider database for the http proxy.

The value for http_proxy is set in the following sqlite database: /data/data/com.android.providers.settings/databases/settings.db

the setting goes in the 'system' table in this database, with a key of 99, a name of 'http_proxy' and a value that is a string containing your proxy server and port. I don't know if you can use a server name rather than just an IP address. I haven't done that.

To set this, on target, do the following:

```
 # cd /data/data/com.android.providers.settings/databases
 # sqlite3 settings.db
SQLite version 3.5.9
Enter ".help" for instructions
sqlite> insert into system values(99,'http_proxy','192.168.1.1:80');
sqlite>.exit 
```

*Replace '192.168.1.1:80' with the appropriate address and port for your network configuration.*

Since it's only a single line, you can also do this from the command line. You can check that the value is stored properly by typing the following:

```
# sqlite3 settings.db "select * from system;" 
```

This should show output similar to the following (first part omitted):

```
...
38|volume_ring|4
39|volume_ring_last_audible|4
99|http_proxy|43.131.3.73:80 
```

This change is persistent, and you should only have to make it once.

## Setting up networking on bootup

Add the following lines in the init.rc:

```
service ethernet /eth0.sh 
```

Now create a file at the root (or anywhere else, probably in /system/etc but modify the above line accordingly) called eth0.sh, make it executable (chmod +x eth0.sh) and put the following lines in it:

```
#!/system/bin/sh

netcfg eth0 dhcp

setprop net.dns1 8.8.8.8
setprop net.dns2 8.8.4.4 
```

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# File Systems

> From: [eLinux.org](http://eLinux.org/Android_File_Systems "http://eLinux.org/Android_File_Systems")

# Android File Systems

## YAFFS

Android in most mobile phones up to version 2.2 (Froyo) use the YAFFS2 filesystem.

Here is some information about YAFFS2:

*   [File Systems#YAFFS2](http://eLinux.org/File_Systems#YAFFS2 "File Systems")
*   Wookey's presentation about YAFFS at ELC Europe 2007 [yaffs.pdf](http://eLinux.org/images/e/e3/Yaffs.pdf "Yaffs.pdf"), [video](http://free-electrons.com/pub/video/2007/elce/elce-2007-wookey-yaffs.ogg)
*   [YAFFS update for ELC Europe 2010](http://wookware.org/talks/yaffsupdate-ELCE-2010.pdf)

## EXT4

According to this report by Ted Tso, version 2.3 (Gingerbread) will be using ext4

[`www.linuxfoundation.org/news-media/blogs/browse/2010/12/android-will-be-using-ext4-starting-gingerbread`](http://www.linuxfoundation.org/news-media/blogs/browse/2010/12/android-will-be-using-ext4-starting-gingerbread)

With ext4, you may need to explicitly sync the data to the file system, in order to make sure not to lose information.

See [this blog entry by Tim Bray](http://android-developers.blogspot.com/2010/12/saving-data-safely.html) for information on this topic.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Android Logging System

> From: [eLinux.org](http://eLinux.org/Android_Logging_System "http://eLinux.org/Android_Logging_System")

# Android Logging System

This article describes the Android logging system

## Contents

*   1 Overview
*   2 Kernel driver
*   3 System and Application logging
    *   3.1 Application log
    *   3.2 Event log
    *   3.3 System log
*   4 'log' command line tool
*   5 Capturing stdout with logwrapper
*   6 Logcat command
    *   6.1 Trick to couple Android logging to Linux kernel logging
*   7 Resources

## Overview

The Android system has a logging facility that allows systemwide logging of information, from applications and system components. This is separate from the Linux kernel's own logging system, which is accessed using 'dmesg' or '/proc/kmsg'. However, the logging system does store messages in kernel buffers.

![](http://eLinux.org/File:Android-logging-kmc-kobayashi.png)

image by Tetsuyuki Kobabayshi, of Kyoto Microcomputer Co.

The logging system consists of:

*   a kernel driver and kernel buffers for storing log messages
*   C, C++ and Java classes for making log entries and for accessing the log messages
*   a standalone program for viewing log messages (logcat)
*   ability to view and filter the log messages from the host machine (via eclipse or ddms)

There are four different log buffers in the Linux kernel, which provide logging for different parts of the system. Access to the different buffers is via device nodes in the file system, in /dev/log.

The four log buffers are:

*   main - the main application log
*   events - for system event information
*   radio - for radio and phone-related informatio
*   system - a log for low-level system messages and debugging

Up until 2010, only the first three logs existed. The system log was created to keep system messages in a separate buffer (outside of '/dev/log/main') so that a single verbose application couldn't overrun system messages and cause them to be lost.

Each message in the log consists of a tag indicating the part of the system or application that the message came from, a timestamp, the message log level (or *priority* of the event represented by the message) and the log message itself.

All of the log buffers except for 'event' use free-form text messages. The 'event' buffer is a 'binary' buffer, where the event messages (and event parameters) are stored in binary form. This form is more compact, but requires extra processing when the event is read from the buffer, as well as a message lookup database, to decode the event strings.

The logging system automatically routes messages with specific tags into the radio buffer. Other messages are placed into their respective buffers when the the log class or library for that buffer is used.

## Kernel driver

The kernel driver for logging is called the 'logger'. See [Android logger](http://eLinux.org/Android_logger "Android logger")

## System and Application logging

### Application log

An Android application includes the [android.util.Log class](http://developer.android.com/reference/android/util/Log.html), and uses methods of this class to write messages of different priority into the log.

Java classes declare their tag statically as a string, which they pass to the log method. The log method used indicates the message "severity" (or log level). Messages can be filtered by tag or priority when the logs are processed by retrieval tools (logcat).

### Event log

Event logs messages are created using [android.util.EventLog class](http://developer.android.com/reference/android/util/EventLog.html), which create binary-formatted log messages. Log entries consist of binary tag codes, followed by binary parameters. The message tag codes are stored on the system at: /system/etc/event-log-tags. Each message has the string for the log message, as well as codes indicating the values associated with (stored with) that entry.

### System log

Many classes in the Android framework utilize the system log to keep their messages separate from (possibly noisy) application log messages. These programs use the android.util.Slog class, with its associated messages.

In all cases, eventually a formatted message is delivered through the C/C++ library down to the kernel driver, which stores the message in the appropriate buffer.

## 'log' command line tool

There is a 'log' command line tool that can be used to create log entries from any program. This is built into the 'toolbox' multi-function program.

The usage for this is:

```
USAGE: log [-p priorityChar] [-t tag] message
       priorityChar should be one of:
               v,d,i,w,e 
```

## Capturing stdout with logwrapper

It is sometimes useful to capture stdout from native applications into the log. There is a utility called 'logwrapper' which can be used to run a program, and redirect it's stdout into log messages.

The logwrapper usage is:

```
Usage: logwrapper [-x] BINARY [ARGS ...]

Forks and executes BINARY ARGS, redirecting stdout and stderr to
the Android logging system. Tag is set to BINARY, priority is
always LOG_INFO.

-x: Causes logwrapper to SIGSEGV when BINARY terminates
    fault address is set to the status of wait() 
```

Source for logwrapper is at: system/core/logwrapper/logwrapper.c

## Logcat command

You can use the 'logcat' command to read the log. This command is located in /system/bin in the local filesystem, or you can access the functionality using the 'adb logcat' command.

Documentation on the use of this command is at: [`developer.android.com/guide/developing/tools/adb.html`](http://developer.android.com/guide/developing/tools/adb.html)

Some quick notes:

*   Log messages each have a tag and priority.
    *   You can filter the messages by tag and log level, with a different level per tag.
*   You can specify (using a system property) that various programs emit their stdout or stderr to the log.

### Trick to couple Android logging to Linux kernel logging

Note that the android logging system is completely separate from the Linux kernel log system (which uses printk inside the kernel to save messages and dmesg to extract them). You can write to the kernel log from user space by writing to /dev/kmsg.

I've seen a reference to couple the two together, to redirect android messages into the kernel log buffer, using the 'logcat' program launched from init, like below:

```
service logcat /system/bin/logcat -f /dev/kmsg
      oneshot 
```

(See [`groups.google.com/group/android-kernel/browse_thread/thread/87d929863ce7c29e/f8b0da9ed6376b2f?pli=1`](http://groups.google.com/group/android-kernel/browse_thread/thread/87d929863ce7c29e/f8b0da9ed6376b2f?pli=1))

I'm not sure why you'd want to do this (maybe to put all messages into a single stream? With the same timestamp, if kernel message timestamping is on?)

## Resources

*   [Android Debug Bridge reference page](http://developer.android.com/guide/developing/tools/adb.html)
    *   has 'adb logcat' usage information
*   [Logging System Of Android](http://blog.kmckk.com/archives/2936958.html) Presentation by Tetsuyuki Kobayashi, September, 2010 at CELF's Japan Technical Jamboree 34

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Android Source Code Description

> From: [eLinux.org](http://eLinux.org/Android_Source_Code_Description "http://eLinux.org/Android_Source_Code_Description")

# Android Source Code Description

Android source code is massive and contains several projects and this is humanly impossible to remember each and every single projects available. This page has been brought to live to enable developers to understand the different part of the source code. This page contains different version of Android and it will outline in detail what each directory contains. Following are the links to the different Android version source code information

*   [android-4.0.3_r1](http://eLinux.org/Android-4.0.3_r1 "Android-4.0.3 r1")

*   [android-4.1.1_r4](http://eLinux.org/Android-4.1.1_r4 "Android-4.1.1 r4")

*   [master-android](http://eLinux.org/Master-android "Master-android")

*   [AOSP Contribution](http://eLinux.org/AOSP_Contribution "AOSP Contribution")

Written and Maintained by : [Nanik Tolaram](https://plus.google.com/+NanikT/)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# 软件开发

# Android 软件开发

关于 Android 软件开发，内容涵盖 SDK、构建系统、工具、调试和测试。

# Software Development Kit

> From: [eLinux.org](http://eLinux.org/Android_SDK "http://eLinux.org/Android_SDK")

# Android SDK

[Android Developers Homepage](http://developer.android.com/index.html)

## Application SDK

Used for creating high level portable Java code.

1.  [Android SDK](http://developer.android.com/sdk/index.html) (Required)
2.  [Eclipse](http://www.eclipse.org/downloads/) Classic or greater (Not technically required
3.  [Eclipse ADT Plugin](http://developer.android.com/sdk/eclipse-adt.html) (Required if using Eclipse)
4.  [Netbeans plugin](http://kenai.com/projects/nbandroid/) (No official support)
5.  [Windows USB Driver](http://developer.android.com/sdk/win-usb.html) (Only need if using Windows)

**Versions** [Regularly Updated Dashboard](http://developer.android.com/resources/dashboard/platform-versions.html)

Top 3 SDK in used by users as of 4/13/2010

1.  1.5
2.  1.6
3.  2.0

## Native SDK

Used to create a portable shared object library file for your C/C++ or asm code inside your Java application

1.  [Android NDK](http://developer.android.com/sdk/ndk/index.html)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Source Build System

> From: [eLinux.org](http://eLinux.org/Android_Build_System "http://eLinux.org/Android_Build_System")

# Android Build System

Basics of the Android Build system were described at (**Down as of 9/2011**): [`source.android.com/porting/build_system.html`](http://source.android.com/porting/build_system.html)

*Note that "partner-setup" should be replaced with "choosecombo" or even "lunch" in that description.*

(*This information seems to be duplicated at:* [`pdk.android.com/online-pdk/guide/build_system.html`](http://pdk.android.com/online-pdk/guide/build_system.html) I'm going to assume that the source.android site is the most up-to-date, but I haven't checked.)

More information about the Android build system, and some of the rationale for it, are described at: [`android.git.kernel.org/?p=platform/build.git;a=blob_plain;f=core/build-system.html`](http://android.git.kernel.org/?p=platform/build.git;a=blob_plain;f=core/build-system.html)

You use build/envsetup.sh to set up a "convenience environment" for working on the Android source code. This file should be source'ed into your current shell environment. After doing so you can type 'help' (or 'hmm') for a list of defined functions which are helpful for interacting with the source.

## Contents

*   1 Overview
*   2 Some Details
    *   2.1 What tools are used
    *   2.2 Telling the system where the Java toolchain is
    *   2.3 Specifying what to build
    *   2.4 Actually building the system
*   3 Build tricks
    *   3.1 Seeing the actual commands used to build the software
    *   3.2 Make targets
    *   3.3 Helper macros and functions
    *   3.4 Speeding up the build
    *   3.5 Building only an individual program or module
    *   3.6 Setting module-specific build parameters
*   4 Makefile tricks
    *   4.1 build helper functions
    *   4.2 Add a file directly to the output area
*   5 Adding a new program to build
    *   5.1 Steps for adding a new program to the Android source tree
*   6 Building the kernel

## Overview

The build system uses some pre-set environment variables and a series of 'make' files in order to build an Android system and prepare it for deployment to a platform.

Android make files end in the extension '.mk' by convention, with the *main* make file in any particular source directory being named 'Android.mk'.

There is only one official file named 'Makefile', at the top of the source tree for the whole repository. You set some environment variables, then type 'make' to build stuff. You can add some options to the make command line (other targets) to turn on verbose output, or perform different actions.

The build output is placed in 'out/host' and 'out/target' Stuff under 'out/host' are things compiled for your host platform (your desktop machine). Stuff under 'out/target/product/\<platform-nameu0005c class="calibre16">' eventually makes it's way to a target device (or emulator).</platform-nameu0005c>

The directory 'out/target/product/\<platform-nameu0005c class="calibre16">/obj' is used for staging "object" files, which are intermediate binary images used for building the final programs. Stuff that actually lands in the file system of the target is stored in the directories root, system, and data, under 'out/target/product/\<platform-nameu0005c class="calibre16">'. Usually, these are bundled up into image files called system.img, ramdisk.img, and userdata.img.</platform-nameu0005c></platform-nameu0005c>

This matches the separate file system partitions used on most Android devices.

## Some Details

### What tools are used

During the build you will be using 'make' to control the build steps themselves. A host toolchain (compiler, linker and other tools) and libraries will be used to build programs and tools that will run on the host. A different toolchain is used to compile the C and C++ code that will wind up on the target (which is an embedded board, device or the emulator). This is usually a "cross" toolchain that runs on an X86 platform, but produces code for some other platform (most commonly ARM). The kernel is compiled as a standalone binary (it does not use a program loader or link to any outside libraries). Other items, like native programs (e.g. init or toolbox), daemons or libraries will link against bionic or other system libraries.

You will be using a Java compiler and a bunch of java-related tools to build most of the application framework, system services and Android applications themselves. Finally, tools are used to package the applications and resource files, and to create the filesystem images that can be installed on a device or used with the simulator.

### Telling the system where the Java toolchain is

Before you build anything, you have to tell the Android build system where your Java SDK is. (Installing a Java SDK is a pre-requisite for building).

Do this by setting a JAVA_HOME environment variable.

### Specifying what to build

In order to decide what to build, and how to build it, the build system requires that some variables be set. Different products, with different packages and options can be built from the same source tree. The variables to control this can be set via a file with declarations of 'make' variables, or can be specified in the environment.

A device vendor can create definition files that describe what is to be included on a particular board or for a particular product. The definition file is called: buildspec.mk, and it is located in the top-level source directory. You can edit this manually to hardcode your selections.

If you have a buildspec.mk file, it sets all the make variables needed for a build, and you don't have to mess with options.

Another method of specifying options is to set environment variables. The build system has a rather ornate method of managing these options for you.

To set up your build environment, you need to load the variables and functions in build/envsetup.sh. Do this by 'source-ing' the file into your shell environment, like this:

```
$ source build/envsetup.sh 
```

or

```
$ . build/envsetup.sh 
```

You can type 'help' (or 'hmm') at this point to see some utility functions that are available to make it easier to work with the source.

To select the set of things you want to build, and what items to build for, you use either the 'choosecombo' function or the 'lunch' function. 'choosecombo' will walk you through the different items you have to select, one-by-one, while 'lunch' allows you select some pre-set combinations.

The items that have to be defined for a build are:

*   the product ('generic' or some specific board or platform name)
*   the build variant ('user', 'userdebug', or 'eng')
*   whether you're running on a simulator ('true' or 'false')
*   the build type ('release' or 'debug')

Descriptions of these different build variants are at [`source.android.com/porting/build_system.html#androidBuildVariants`](http://source.android.com/porting/build_system.html#androidBuildVariants)

The build process from a user perspective is described pretty well in this blog post: [First Android platform build](http://blog.codepainters.com/2009/12/18/first-android-platform-build/) by CodePainters, December 2009

### Actually building the system

Once you have things set up, you actually build the system with the 'make' command.

To build the whole thing, run 'make' in the top directory. The build will take a long time, if you are building everything (for example, the first time you do it).

## Build tricks

### Seeing the actual commands used to build the software

Use the "showcommands" target on your 'make' line:

```
$ make -j4 showcommands 
```

This can be used in conjunction with another make target, to see the commands for that build. That is, 'showcommands' is not a target itself, but just a modifier for the specified build.

In the example above, the -j4 is unrelated to the showcommands option, and is used to execute 4 make sessions that run in parallel.

### Make targets

Here is a list of different make targets you can use to build different parts of the system:

*   make sdk - build the tools that are part of an SDK (adb, fastboot, etc.)
*   make snod - build the system image from the current software binaries
*   make services
*   make runtime
*   make droid - make droid is the normal build.
*   make all - make everything, whether it is included in the product definition or not
*   make clean - remove all built files (prepare for a new build). Same as rm -rf out/\<configurationu0005c class="calibre16">/</configurationu0005c>
*   make modules - shows a list of submodules that can be built (List of all LOCAL_MODULE definitions)
*   make \ <localu0005c_moduleu0005c class="calibre16">- make a specific module (note that this is not the same as directory name. It is the LOCAL_MODULE definition in the Android.mk file)</localu0005c_moduleu0005c>
*   make clean-\ <localu0005c_moduleu0005c class="calibre16">- clean a specific module</localu0005c_moduleu0005c>
*   make bootimage TARGET_PREBUILT_KERNEL=/path/to/bzImage - create a new boot image with custom bzImage

### Helper macros and functions

There are some helper macros and functions that are installed when you source envsetup.sh. They are documented at the top of envesetup.sh, but here is information about a few of them:

*   croot - change directory to the top of the tree
*   m - execute 'make' from the top of the tree (even if your current directory is somewhere else)
*   mm - builds all of the modules in the current directory
*   mmm \ <dir1u0005c class="calibre16">... - build all of the modules in the supplied directories</dir1u0005c>
*   cgrep \ <patternu0005c class="calibre16">- grep on all local C/C++ files</patternu0005c>
*   jgrep \ <patternu0005c class="calibre16">- grep on all local Java files</patternu0005c>
*   resgrep \ <patternu0005c class="calibre16">- grep on all local res/*.xml files</patternu0005c>
*   godir \ <filenameu0005c class="calibre16">- go to the directory containing a file</filenameu0005c>

### Speeding up the build

You can use the '-j' option with make, to start multiple threads of make execution concurrently.

In my experience, you should specify about 2 more threads than you have processors on your machine. If you have 2 processors, use 'make -j4', If they are hyperthreaded (meaning you have 4 virtual processors), try 'make -j6.

You can also specify to use the 'ccache' compiler cache, which will speed up things once you have built things a first time. To do this, specify 'export USE_CCACHE=1' at your shell command line. (Note that ccache is included in the prebuilt section of the repository, and does not have to be installed on your host separately.)

### Building only an individual program or module

If you use build/envsetup.sh, you can use some of the defined functions to build only a part of the tree. Use the 'mm' or 'mmm' commands to do this.

The 'mm' command makes stuff in the current directory (and sub-directories, I believe). With the 'mmm' command, you specify a directory or list of directories, and it builds those.

To install your changes, do 'make snod' from the top of tree. 'make snod' builds a new system image from current binaries.

### Setting module-specific build parameters

Some code in Android system can be customized in the way they are built (separate from the build variant and release vs. debug options). You can set variables that control individual build options, either by setting them in the environment or by passing them directly to 'make' (or the 'm...' functions which call 'make'.)

For example, the 'init' program can be built with support for bootchart logging by setting the INIT_BOOTCHART variable. (See [Using Bootchart on Android](http://eLinux.org/Using_Bootchart_on_Android "Using Bootchart on Android") for why you might want to do this.)

You can accomplish either with:

```
$ touch system/init/init.c
$ export INIT_BOOTCHART=true
$ make 
```

or

```
$ touch system/init/init.c
$ m INIT_BOOTCHART=true 
```

## Makefile tricks

These are some tips for things you can use in your own Android.mk files.

### build helper functions

A whole bunch of build helper functions are defined in the file build/core/definitions.mk

Try grep define build/core/definitions.mk for an exhaustive list.

Here are some possibly interesting functions:

*   print-vars - shall all Makefile variables, for debugging
*   emit-line - output a line during building, to a file
*   dump-words-to-file - output a list of words to a file
*   copy-one-file - copy a file from one place to another (dest on target?)

### Add a file directly to the output area

You can copy a file directly to the output area, without building anything, using the add-prebuilt-files function.

The following line, extracted from prebuilt/android-arm/gdbserver/Android.mk copies a list of files to the EXECUTABLES directory in the output area:

```
$(call add-prebuilt-files, EXECUTABLES, $(prebuilt_files)) 
```

## Adding a new program to build

### Steps for adding a new program to the Android source tree

*   make a directory under 'external'
    *   e.g. ANDROID/external/myprogram
*   create your C/cpp files.
*   create Android.mk as clone of external/ping/Android.mk
*   Change the names ping.c and ping to match your C/cpp files and program name
*   add the directory name in ANDROID/build/core/main.mk after external/zlib as external/myprogram
*   make from the root of the source tree
*   your files will show up in the build output area, and in system images.
    *   You can copy your file from the build output area, under out/target/product/..., if you want to copy it individually to the target (not do a whole install)

See [`www.aton.com/android-native-development-using-the-android-open-source-project/`](http://www.aton.com/android-native-development-using-the-android-open-source-project/) for a lot more detail.

## Building the kernel

The kernel is "outside" of the normal Android build system (indeed, the kernel is not included by default in the Android Open Source Project). However, there are tools in AOSP for building a kernel. If you are building the kernel, start on this page: [`source.android.com/source/building-kernels.html`](http://source.android.com/source/building-kernels.html)

If you are building the kernel for the emulator, you may also want to look at: [`stackoverflow.com/questions/1809774/android-kernel-compile-and-test-with-android-emulator`](http://stackoverflow.com/questions/1809774/android-kernel-compile-and-test-with-android-emulator)

And, Ron M wrote (on the android-kernel mailing list, on May 21, 2012):

This post is very old - but nothing has changes as far as AOSP is concerned, so in case anyone is interested and runs into this problem when building for QEMU:

There is actually a nice and shorter way to build the kernel for your QEMU target provided by the AOSP:

1.  cd to your kernel source dir (Only goldfish 2.6.29 works out of the box for the emulator)

2.  ${ANDROID_BUILD_TOP}/external/qemu/distrib/build-kernel.sh -j=64 --arch=x86 --out=$YourOutDir

3.  emulator -kernel ${YourOutDir}/kernel-qemu # run emulator:

Step #2 calls the toolbox.sh wrapper scripts which works around the SSE disabling gcc warning - which happens for GCC \< 4.5 (as in the AOSP prebuilt X86 toolchain).

This script adds the " -mfpmath=387 -fno-pic" in case it is an X86 and that in turn eliminates the compilation errors seen above.

To have finer control over the build process, you can use the "toolbox.sh" wrapper and set some other stuff without modifying the script files.

An example for building the same emulator is below:

```
# Set arch
export ARCH=x86
# Have make refer to the QEMU wrapper script for building android over x86
(eliminates the errors listed above)
export
CROSS_COMPILE=${ANDROID_BUILD_TOP}/external/qemu/distrib/kernel-toolchain/android-kernel-toolchain-
# Put your cross compiler here. I am using the AOSP prebuilt one in this example
export
REAL_CROSS_COMPILE=${ANDROID_BUILD_TOP}/prebuilt/linux-x86/toolchain/i686-android-linux-4.4.3/bin/i686-android-linux-
# Configure your kernel - here I am taking the default goldfish_defconfig
make goldfish_defconfig
# build
make -j64
# Run emulator:
emulator -kernel arch/x86/boot/bzImage -show-kernel 
```

This works for the 2.6.29 goldfish branch. If anyone is using the emulator with a 3+ kernel I would be like to hear about it.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Application Development Resources

> From: [eLinux.org](http://eLinux.org/Android_Application_Development "http://eLinux.org/Android_Application_Development")

# Android Application Development

For the most reliable reference to the Android Application Development go through:

## [`developer.android.com/guide/index.html`](http://developer.android.com/guide/index.html)

This includes java doc to all classes, packages, and other variables and is the most updated site.

Though it is most customary to get started in Eclipse, which is the most standard, you are also free to use Netbeans, but the Netbeans updates for the Android SDK plugin is not very dynamic and to my knowledge was developed and supported by a single project called Kenai.

You should also know that you can not step into Android Application Development with out having base on Core Java programming, yes, that is a pre-requisite, else it is bit difficult.

So then, welcome, get ready to jump into the droid community and get going.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Scripting

> From: [eLinux.org](http://eLinux.org/Android_Scripting "http://eLinux.org/Android_Scripting")

# Android Scripting

SL4A (Scripting Layer for Android) allowing you to edit and execute scripts and interactive interpreters directly on the Android device. Many parts of the Android API are available, directly from the scripting language.

Scripts can be run interactively in a terminal or associated with an icon and launched like regular apps.

Home page: [`code.google.com/p/android-scripting/`](http://code.google.com/p/android-scripting/)

The [SL4A User's Guide](http://code.google.com/p/android-scripting/wiki/UserGuide) is a good place to start.

SL4A supports the following languages

*   Python
*   Perl
*   JRuby
*   Lua
*   BeanShell
*   JavaScript
*   Tcl
*   Shell scripts

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Debugging

> From: [eLinux.org](http://eLinux.org/Android_Debugging "http://eLinux.org/Android_Debugging")

# Android Debugging

Debugging methods for Android

## Contents

*   1 Debuggers
    *   1.1 Kernel and User co-debug with GDB on Android
*   2 loggers
    *   2.1 kernel message log
        *   2.1.1 init logging
    *   2.2 Android logging system
*   3 tracers
    *   3.1 strace
    *   3.2 Dalvik Method Tracer
*   4 Additional Resources

## Debuggers

### Kernel and User co-debug with GDB on Android

This presentation covers lots of Android debug resources provided by Linaro, presented by Zach Pfeffer in Spring 2012\. Included is information about how to debug kernel and user simultaneously with gdb.

See [Media:Zach Pfeffer Next Gen Android 2012.pdf](http://eLinux.org/images/4/4e/Zach_Pfeffer_Next_Gen_Android_2012.pdf "Zach Pfeffer Next Gen Android 2012.pdf")

## loggers

### kernel message log

The Linux kernel has a message log in an internal ring buffer. You can access the contents of this log using the ['dmesg'](http://en.wikipedia.org/wiki/Dmesg) command.

You can add timing information to the printk messages, by adding "time" to the Linux kernel command line.

#### init logging

The Android init program outputs some messages to the kernel log, as it starts the system. You can increase the verbosity of init, using the "loglevel" command in the /init.rc file.

The default loglevel is 3, but you can change it to 8 (the highest) by changing the following line in the /init.rc file. Change:

```
loglevel 3 
```

to

```
loglevel 8 
```

### Android logging system

The Android application framework has a built-in logging system, which goes through a special driver in the kernel. It is described at:[`developer.android.com/guide/developing/tools/adb.html#logcat`](http://developer.android.com/guide/developing/tools/adb.html#logcat)

Note that although the log dumper (logcat) is described on the Android developer site on the 'adb' page, there is a native logcat command included in the Android distribution (that is, available on the target file system, which can be run locally).

## tracers

### strace

You can use [strace](http://en.wikipedia.org/wiki/Strace) on Android. It is included in the Android open source project (at least as of Android 2.1), and appears to be automatically installed in engineering builds of the software.

To use strace during early initialization, you can put it in the /init.rc file. For example, to trace zygote initialization, change the following line in /init.rc.

```
service zygote /system/bin/app_process -Xzygote /system/bin --zygote --start-system-server 
```

should be changed to:

```
service zygote /system/xbin/strace -tt -o/data/boot.strace /system/bin/app_process -Xzygote /system/bin --zygote --start system-server 
```

### Dalvik Method Tracer

See [`developer.android.com/guide/developing/tools/traceview.html`](http://developer.android.com/guide/developing/tools/traceview.html)

## Additional Resources

*   [Google I/O 2009 - Debugging Arts of the Ninja Masters (video)](http://www.youtube.com/watch?v=Dgnx0E7m1GQ&feature=related) by Justin Mattson

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Testing

> From: [eLinux.org](http://eLinux.org/Android_Testing "http://eLinux.org/Android_Testing")

# Android Testing

This page has information about various tests you can perform on Android

Google provides a number of resources for testing applications, including monkey, the logger, and the compliance test suite (CTS)

For an overview of Android application testing and resources provided by Google, see: [`developer.android.com/guide/topics/testing/index.html`](http://developer.android.com/guide/topics/testing/index.html)

## Contents

*   1 Android Test Framework
*   2 Application testing resources
*   3 Benchmarks
*   4 Compliance Test Suite

## Android Test Framework

Google provides an integrated test framework for testing Android applications, based on the JUnit test framework from java.

See [Android Testing Fundamentals](http://developer.android.com/guide/topics/testing/testing_android.html)

## Application testing resources

*   [monkeyrunner](http://developer.android.com/guide/developing/tools/monkeyrunner_concepts.html)

    *   The monkeyrunner tool provides an API for writing programs that control an Android device or emulator from outside of Android code. It allows you to write a program, which runs on your host machine that can interact with an application running in the emulator or on a target device.
    *   With monkeyrunner, you can write a Python program that installs an Android application or test package, runs it, sends keystrokes to it, takes screenshots of its user interface, and stores screenshots on the host.
*   [Monkey](http://developer.android.com/guide/developing/tools/monkey.html) is a user interface and application tester for Android applications.

    *   It is a command-line tool that sends pseudo-random streams of keystrokes, touches, and gestures to a device.
    *   This tool in unrelated to the monkeyrunner tool mentioned above. (It runs on the target, and monkeyrunner-based programs run on the development host machine.)
*   [Robotium](http://code.google.com/p/robotium/) test framework

    *   Robotium is a test framework created to make it easy to write powerful and robust automatic black-box test cases for Android applications. With the support of Robotium, test case developers can write function, system and acceptance test scenarios, spanning multiple Android activities.
    *   Robotium has full support for Activities, Dialogs, Toasts, Menus and Context Menus.
*   [Roboelectric](http://pivotal.github.com/robolectric) test framework

    *   Roboelectric allow you to test-drive the development of your Android app inside the JVM on your workstation in seconds, instead of in the emulator on on a device (which can be slow)
    *   Robolectric allows you to test most Android functionality including layouts and GUI behavior, services, and networking code. It has more flexibility than Google's testing framework in some areas.
*   [Ranorex](http://www.ranorex.com/mobile-automation-testing/android-test-automation.html) test framework

    *   Ranorex provides a .Net based test automation framework which allows to record test scenarios directly on real mobile devices as well as on desktop machines.
    *   Ranorex easily instruments the apk and deploys the instrumetned apk directly on the devices, starts the application automatically, performs all recorded actions (keystrokes, touch events, device button events, validations) and closes the application automatically.

## Benchmarks

There are a number of benchmarks you can use to test operating system, hardware and graphics performance.

For information about performance testing and benchmarks, see: [Android Benchmarks](http://eLinux.org/Android_Benchmarks "Android Benchmarks")

## Compliance Test Suite

Google provides a suite of tests to validate that a device complies with the Android standard.

See [Android Compliance Test Suite](http://eLinux.org/Android_Compliance_Test_Suite "Android Compliance Test Suite") for more informaton.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# 基于 Android 的系统

介绍与 Android 产品相关的信息，内容涵盖 Android 移植、如何获得系统 Root 权限、Android 应用开发、各类派生 ROM、Android 模拟器以及相关开发和测试等。

# Products (announced & shipped)

> From: [eLinux.org](http://eLinux.org/Android_Hardware "http://eLinux.org/Android_Hardware")

# Android Hardware

## Contents

*   1 Android Devices
*   2 Mobile Phones
    *   2.1 That have shipped
    *   2.2 Under development
*   3 Non-phone devices
    *   3.1 That have shipped
    *   3.2 Under development
*   4 Development board

## Android Devices

It looks like there's a page on this on wikipedia: [`wikipedia.org/wiki/List_of_Android_devices`](http://wikipedia.org/wiki/List_of_Android_devices)

[Hello Android](http://www.helloandroid.com/devices) has a comprehensive database as well, along with an Android app to view the database on your phone.

## Mobile Phones

See [`www.androphones.com/`](http://www.androphones.com/)

### That have shipped

*   HTC Dream
    *   Branded as the G1 by T-Mobile
    *   Google: Android Developer Phone 1 (ADP1) - same hardware as G1, but unlocked.
*   HTC G1 V2
*   HTC G2, aka HTC Magic
    *   Google Android dev phone 2 - same hardware as Magic, but unlocked
*   HTC Hero
    *   branded as T-Mobile G2
*   HTC Hero for CDMA
    *   available from Sprint on Oct 11, 2009
*   HTC Tatoo - Europe, Oct 2009
    *   SPECS: 528MHz Qualcomm processor, 512MB ROM and 256MB RAM, 240x320 px (QVGA) 2.8-inch touchscreen, GPS, WiFi, compass and accelerometer.
    *   HTC's Sense UI in TATTOO: [`www.akihabaranews.com/en/news-18854-TATTOO%2C+HTC%E2%80%99s+Latest+Android+Phone+with+Sense+UI.html`](http://www.akihabaranews.com/en/news-18854-TATTOO%2C+HTC%E2%80%99s+Latest+Android+Phone+with+Sense+UI.html)
*   Samsung Galaxy (in Europe)
*   Samsung Behold II
    *   Samsung's TouchWiz 3D Interface: [`www.akihabaranews.com/en/news-19025-Samsung+Behold+II%2C+TouchWiz+Wonder+for+T-Mobile.html`](http://www.akihabaranews.com/en/news-19025-Samsung+Behold+II%2C+TouchWiz+Wonder+for+T-Mobile.html)
*   Motorola Droid (on Verizon)
    *   branded as Motorola Milestone in Europe
*   Motorola Cliq
    *   Has "MotoBlur" interface for interacting with social network sites
    *   [Wired Article Sep 2009](http://www.wired.com/gadgetlab/2009/09/motorola-android/)
*   Motorola Sholes ?
*   Sony Ericsson XPERIA 10
    *   [`gizmodo.com/5395712/sony-ericsson-xperia-x10-announced-sonys-first-android-device`](http://gizmodo.com/5395712/sony-ericsson-xperia-x10-announced-sonys-first-android-device)
*   Sony Ericsson X10 Mini
    *   [`www.sonyericsson.com/cws/companyandpress/aboutus/awards/award/x10miniaward?cc=gb&lc=en`](http://www.sonyericsson.com/cws/companyandpress/aboutus/awards/award/x10miniaward?cc=gb&lc=en)

### Under development

*   Acer Liquid
    *   [`www.linuxfordevices.com/c/a/News/Acer-Liquid/`](http://www.linuxfordevices.com/c/a/News/Acer-Liquid/)
    *   [`www.akihabaranews.com/en/news-19112-Acer+Finally+Announces+Their+Android+Liquid+Smartphone.html`](http://www.akihabaranews.com/en/news-19112-Acer+Finally+Announces+Their+Android+Liquid+Smartphone.html)
*   HTC Fiesta ?
*   LG GW620
    *   [Engadget Article Sep 2009](http://www.engadget.com/2009/09/14/lg-officially-announces-gw620-its-first-android-phone/)
    *   [Wired article Sep 2009](http://www.wired.com/gadgetlab/2009/09/lg-android-phone/)
    *   LG's slide out QWERTY: [`www.akihabaranews.com/en/news-18892-LG-GW620%2C+LG%E2%80%99s+First+Android+Smartphone.html`](http://www.akihabaranews.com/en/news-18892-LG-GW620%2C+LG%E2%80%99s+First+Android+Smartphone.html)

## Non-phone devices

### That have shipped

*   [OpenSourceMID.org](http://www.opensourcemid.org) K7 OMAP3530 MID tablet Opensourcemid
*   Archos Internet tablets

    *   Archos 5 and Archos 7 Internet tablets - [Android tablet plays 720p video](http://www.linuxfordevices.com/c/a/News/Archos-5-Internet-Tablet/?kc=LNXDEVNL091609) Linux For Devices Article, Sep 2009
*   Android handheld game device with Cortex-A8 833Mhz powerful SoC

    *   ODROID - [Hackable Android handheld game device](http://www.linuxfordevices.com/c/a/News/HardKernel-Odroid/) Linux For Devices Article, Dec 2009
*   S3C6410 microcontroller
    *   [Android on S3C6410](http://elinux.org/Android_on_S3C6410) **

### Under development

*   [Continental AG car navigation system](http://phandroid.com/2009/06/05/android-in-your-car-autolinq-by-continental-ag/)

*   [Marvel pad with targeted $100 price](http://www.linuxfordevices.com/c/a/News/Marvell-Moby/?kc=LNXDEVNL032410)

*   Google TV

## Development board

*   [Devkit8500D](http://www.armkits.com/Product/devkit8500d.asp) TI DM3730 based board
*   [Devkit7000](http://www.armkits.com/Product/devkit7000.asp) Samsung S5PV210 based board
*   [Mini6410](http://www.minidevs.com/minidevs/Mini6410%20Board.html) [Android_on_S3C6410](http://eLinux.org/Android_on_S3C6410 "Android on S3C6410")
*   [MYD-AM335X](http://www.myirtech.com/list.asp?id=466) TI AM335x Development Board

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Porting efforts and issues

> From: [eLinux.org](http://eLinux.org/Android_Porting "http://eLinux.org/Android_Porting")

# Android Porting

This page describes various effort to port Android to new boards and new processors

## Contents

*   1 Porting Overview
*   2 Porting Tutorials
*   3 Porting Issues
    *   3.1 Android Hardware Abstraction Layer
*   4 Porting to New Processors
*   5 Virtualization environments

## Porting Overview

This overview of porting steps was seen on the android-porting list: See [`www.mail-archive.com/android-porting@googlegroups.com/msg06721.html`](http://www.mail-archive.com/android-porting@googlegroups.com/msg06721.html)

This glosses over all the kernel work for a new board, and the android-specific kernel patches, but has some good discussion about the flash partitioning and file system bringup process.

```
If the linux kernel is up and running with all drivers in.
(particularly touchscreen and display) it shouldn't be too bad.

IHMO, the easiest way to get you running is to aggregate the initial
ramfs built into the kernel with the Android build, the root Android
root filesystem (system), and the user data section (mounted as /data
I believe) into one root filesystem.

You can then take that root filesystem as one tarball.

Modify the NAND partitioning of the kernel to set aside space for the
whole Android rootfs, and of course rebuild the kernel. (Be sure yaffs
support is in the kernel) Also no need for a ramfs at this point, just
have the kernel look to mtd2 for it's root filesystem, which will be
jffs2

Create yourself a busybox root filesystem too. Make that into a jffs2
image.

So your partitioning would look something similar to this (you'll have
to decide on the sizes of course):
mtd0: bootloader
mtd1: kernel
mtd2: rootfs (jffs2)
mtd3: Android rootfs.

Erase everything on the NAND.
Burn the normal Chumby bootloader to mtd0.
Burn the the new kernel into mtd1.
Burn the jffs2 rootfs image to mtd2.

Boot the device. Hopefully you get yourself to a prompt.

Once you have that prompt mount mtd3 to /mnt/android as a yaffs2
partition.
Untar your Android rootfs into /mnt/android.
Chown and chgrp everything under /mnt/android to "root"
chroot to that mount point "chroot /mnt/android /init"

At this point you should see Android trying to run.

I know that's a bit to chomp on, but it's more of an outline of what
you will need to do. Of course it's assuming you have the ability to
erase the whole nand and put down images amongst other assumptions,
but it should help get your mind around a little bit of the
requirements to get Android running on your device.

Regarding your bootloader question, I'd just stick with the current
one. You'll only need to modify that if/when you go into having
everything compatible with the recovery system. Which is a completely
different discussion. 
```

## Porting Tutorials

*   [Porting Android to a new Device](http://www.linuxfordevices.com/c/a/Linux-For-Devices-Articles/Porting-Android-to-a-new-device/)
    *   excellent and thorough paper on porting Android to the Nokia N810.
    *   Has a detailed list of kernel changes and annotated diffs.
*   [`wiki.kldp.org/wiki.php/AndroidPortingOnRealTarget`](http://wiki.kldp.org/wiki.php/AndroidPortingOnRealTarget)
*   [Android on OMAP](http://eLinux.org/Android_on_OMAP "Android on OMAP") - excellent tutorial covering lots of different issues for porting Android to platforms based on the TI OMAP (ARM) processor
*   Some cursory notes on a port to a PXA board are at: [`letsgoustc.spaces.live.com/blog/cns!89AD27DFB5E249BA!320.entry`](http://letsgoustc.spaces.live.com/blog/cns!89AD27DFB5E249BA!320.entry)
*   Adding a new device or changing the configuration of an existing device [Android Device](http://eLinux.org/Android_Device "Android Device")

## Porting Issues

*   Matt Porter (Mentor Graphics) gave a presentation on difficulties encountered while they were porting Android to MIPS and PPC processors at [ELC Europe 2009](http://www.embeddedlinuxconference.com/elc_europe09/index.html). His talk was called "Mythbusters: Android" and has lots of good information.

    *   See [Mythbusters_Android.pdf](http://eLinux.org/images/2/2d/Mythbusters_Android.pdf "Mythbusters Android.pdf")
*   [Dalvik porting guide](http://android.git.kernel.org/?p=platform/dalvik.git;a=blob_plain;f=docs/porting-guide.html;hb=HEAD)

*   Matthias Brugger presented his personal "war story" on porting Android at ELC Europe 2012\. See his [slides](http://www.slideshare.net/MatthiasBrugger/porting-android-40toacustomboard) on slideshare.

### Android Hardware Abstraction Layer

Android talks to standard devices through its hardware abstraction layer, which overlays the kernel interfaces to devices (e.g. devices nodes, Linux system calls, etc.). To add support for your own hardware, or, in particular, to add support to Android for some new type of hardware, you need to understand this abstraction layer.

Karim Yaghmour has a good blog entry describing the Android HAL layer: [`www.opersys.com/blog/extending-android-hal`](http://www.opersys.com/blog/extending-android-hal)

## Porting to New Processors

*   Mentor Graphics has ported Android to MIPS and PPC
*   Power.Org supported the work to port Android to PPC
    *   Nina Wilner talked about this work, and gave a demo at ELC Europe 2009
    *   see [Android_On_Power.pdf](http://eLinux.org/images/0/07/Android_On_Power.pdf "Android On Power.pdf")

## Virtualization environments

There are available some virtualization environments, which allow Android applications (or the whole system) to run on other Linux-based systems, such as MeeGo or Ubuntu.

Here is some information about different systems known to exist:

*   OpenMobile ACL (Application Compatibility Layer)
    *   LinuxDevices article: [`www.linuxfordevices.com/c/a/News/OpenMobile-ACL-for-MeeGo/?kc=LNXDEVNL092811`](http://www.linuxfordevices.com/c/a/News/OpenMobile-ACL-for-MeeGo/?kc=LNXDEVNL092811)
    *   OpenMobile product page:[`openmobile.co/products.php`](http://openmobile.co/products.php)
*   Myriad Alien Dalvik
    *   LinuxDevices article: [`www.linuxfordevices.com/c/a/News/Myriad-Group-Myriad-Alien-Dalvik/`](http://www.linuxfordevices.com/c/a/News/Myriad-Group-Myriad-Alien-Dalvik/)
    *   Myried Group page: [`www.myriadgroup.com/Device-Manufacturers/Android-solutions/~/media/D42B513FB5114FF2B4CA13A2D8CE313E.ashx`](http://www.myriadgroup.com/Device-Manufacturers/Android-solutions/~/media/D42B513FB5114FF2B4CA13A2D8CE313E.ashx)
*   [FIXTHIS - should add tetsuyuki presentation about running Android on Ubuntu here]

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Getting Root (Jailbreaking)

> From: [wikipedia.org](https://en.wikipedia.org/wiki/Rooting_(Android_OS))

# Getting Root (Jailbreaking)

> Rooting is the process of allowing users of smartphones, tablets and other devices running the Android mobile operating system to attain privileged control (known as root access) over various Android's subsystems. As Android uses the Linux kernel, rooting an Android device gives similar access to administrative permissions as on Linux or any other Unix-like operating system such as FreeBSD or OS X.
> 
> Rooting is often performed with the goal of overcoming limitations that carriers and hardware manufacturers put on some devices. Thus, rooting gives the ability (or permission) to alter or replace system applications and settings, run specialized apps that require administrator-level permissions, or perform other operations that are otherwise inaccessible to a normal Android user. On Android, rooting can also facilitate the complete removal and replacement of the device's operating system, usually with a more recent release of its current operating system.
> 
> Root access is sometimes compared to jailbreaking devices running the Apple iOS operating system. However, these are different concepts. Jailbreaking describes the bypass of several types of Apple prohibitions for the end user: modifying the operating system (enforced by a "locked bootloader"), installing non-officially approved apps via sideloading, and granting the user elevated administration-level privileges. Only a minority of Android devices lock their bootloaders, and many vendors such as HTC, Sony, Asus and Google explicitly provide the ability to unlock devices, and even replace the operating system entirely.[1][2][3] Similarly, the ability to sideload apps is typically permissible on Android devices without root permissions. Thus, it is primarily the third aspect of iOS jailbreaking relating to giving users superuser administrative privileges that most directly correlates to Android rooting.

# Miscellaneous Hardware Fixes

> From: [eLinux.org](http://eLinux.org/Android_Hardware_Fixes "http://eLinux.org/Android_Hardware_Fixes")

# Android Hardware Fixes

## Fixing Android Hardware

### G1 hardware fixes

#### Acceleromters stopped working

If your acceleromters stop working, try the following:

*   Try removing the file "akmd_set.txt" can wake it back up. Word is:
    *   Remove /data/misc/akmd_set.txt and reboot your phone (or kill akmd process)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Android x86

> From: [eLinux.org](http://eLinux.org/Android_x86 "http://eLinux.org/Android_x86")

# Android x86

Android x86 is a software port to normal **pc** s. [Android x86 Homepage](http://www.android-x86.org/)

The current stable release is [`www.android-x86.org/releases/releasenote-3-2-rc2`](http://www.android-x86.org/releases/releasenote-3-2-rc2) Android-x86 3.2-r2], based upon Honeycomb.

## Contents

*   1 ICS 4.0.3
    *   1.1 Build on ubuntu 11.10
    *   1.2 Hardware
*   2 DONUT 1.6 r2
*   3 short steps to get a running system
    *   3.1 Running the system from boot medium CD or USB-Stick
    *   3.2 run the system from harddisk
*   4 details for a running system
    *   4.1 SD-card
    *   4.2 navigation
    *   4.3 sound
    *   4.4 video
    *   4.5 application errors
*   5 Development
    *   5.1 improvements/request for changes
    *   5.2 errors
    *   5.3 shell
    *   5.4 navigation/keyboard
    *   5.5 File Infos
    *   5.6 hand made changes on an usb-Stick Android 1.6 r2
    *   5.7 sound
    *   5.8 yaffs2 - filesystem
    *   5.9 Links

## ICS 4.0.3

### Build on ubuntu 11.10

see [Building Android 4.0 on Ubuntu 11.10](http://www.android-dev.ro/2011/12/13/building-android-4-0-on-ubuntu-11-10/)

install old version of gcc 4.4 with : sudo apt-get install gcc-4.4 g++-4.4 g++-4.4-multilib gcc-4.4-multilib

run make with : make CC=gcc-4.4 CXX=g++-4.4 -j4 iso_img TARGET_PRODUCT=generic_x86

### Hardware

Fritz!Wlan

AVM GmbH AVM Fritz!WLAN N [Atheros AR9001U]

## DONUT 1.6 r2

## short steps to get a running system

### Running the system from boot medium CD or USB-Stick

*   download CD-Image [android-x86-1.6-r2.iso](http://www.android-x86.org/download) or USB-Image [android-x86-1.6-r2_usb.img.gz](http://www.android-x86.org/download)
*   burn cd image or for the usb-image use the following commands on a linux box "gunzip [`www.android-x86.org/download`](http://www.android-x86.org/download)" and "dd if=android-x86-1.6-r2_usb.img of=/dev/sda" (of=/dev/sda is depending on where your linux mounted your usb-stick)
*   boot from created medium and choose the first menu entry "Live USB - Run Android-x86 without Installation"
*   in android goto settings/sound & display/screen timeout and set to "never timeout"

### run the system from harddisk

s. as well the [installation section on android-x86.org](http://www.android-x86.org/)

*   boot from CD or USB-Stick android x86 boot menu choose the fourth option "Installation - Install Android-x86 1.6-r2 to harddisk"
*   select "Create/Modify partitions" and create a bootable partition with cfdisk
*   select created partition e.g. sda1 and format with e.g. ext3
*   install GRUB by selecting 'yes'
*   reboot system and boot from harddisk and select the default menu entry "Android-x86 1.6-r2"
*   in android goto settings/sound & display/screen timeout and set to "never timeout"

## details for a running system

### SD-card

for mounting an sd card see [[1]](http://www.android-x86.org/documents/sdcardhowto)

### sound

**Keys on ASUS EeePC**

```
Fn-F7, F8, F9,
some models are
Fn-F10, F11, F12 
```

**Notebooks**

```
Some notebooks also have volume adjustment hotkeys 
```

**raise sound volume in shell**

```
* change screen to console 1, press Alt+F1
* enter
  "alsa_amixer cset numid=1 31" for 'Front Playback Volume' and/or
  "alsa_amixer cset numid=20 31" for 'Master Playback Volume'  and/or
  "alsa_amixer cset numid=3 31" for 'Speaker Playback Volume'
* go back to graphic screen,  press Alt+F7 
```

**x86 PCs with normal keyboard**

rear panel audio jack and front panel audio jack depend on the setting of 'Front Playback Volume' alsa_amixer sound setting

### video

### application errors

*   menu /settings/about phone/System tutorial -> Sorry! The application settings (process com.android.settings) has stopped unexpectedly. Please try again.

## Development

### improvements/request for changes

*   set "settings/sound & display/screen timeout" to "never timeout" as for an x86 system there is no need to timeout an new users don't know what happens.

### errors

### shell

### navigation/keyboard

keyboard layouts see /system/usr/keylayout/

command to get events: getevent

[Keymaps and Keyboard Input, a detailed description](http://www.kandroid.org/android_pdk/keymaps_keyboard_input.html)

### File Infos

1.  system.sfs - squash filesystem
2.  system.img - ext2 file Image
3.  ramdisk.img - gzip cpio file - extract in an empty folder with "gzip -d \< ramdisk.img |cpio -id"
4.  initrd.img - gzip cpio file - extract in an empty folder with "gzip -d \< initrd.img |cpio -id"

### hand made changes on an usb-Stick Android 1.6 r2

**File content of an usb-stick**

```
├── android-system
│   ├── initrd.img
│   ├── install.img
│   ├── kernel
│   ├── ramdisk.img
│   └── system.sfs
├── android-x86.xpm.gz
├── cmdline
├── grub4dos
├── kernel -> grub4dos
├── lost+found
├── menu.lst
└── ramdisk
.
2 directories, 11 files 
```

steps to change files in system.sfs (system.img)

```
* Ubuntu 10.4 box
* change to shell, press strg+alt+F1
* sudo -i
* aptitude and install squashfs-tools
* modprobe squashfs
* cd /home/administrator
* copy system.sfs (squash file system) to harddisk, in /home/administrator
* mkdir systemsfs
* mount ./system.sfs ./systemsfs -t squashfs -o loop
* copy ./systemsfs/system.img /home/administrator/
* mkdir systemimg
* mount ./system.img ./systemimg -t ext2 -o loop 
```

now cd to systemimg directory and make the changes

eg. change *.kl files for sound F7 (scanncode=65) = mute; F8 (scanncode=66) = volume_down; F9 (scanncode=67) = volume_up

```
* cd usr/keylayout
* vi *.kl
* change lines
  key 113   VOLUME_MUTE
  key 114   VOLUME_DOWN
  key 115   VOLUME_UP

  key 65   VOLUME_MUTE
  key 66   VOLUME_DOWN
  key 67   VOLUME_UP 
```

### sound

s.

1.  [`wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture`](http://wiki.archlinux.org/index.php/Advanced_Linux_Sound_Architecture)
2.  [`www.alsa-project.org/main/index.php/Main_Page`](http://www.alsa-project.org/main/index.php/Main_Page)

### yaffs2 - filesystem

```
* download unyaffs2 http://code.google.com/p/yaffs2utils/downloads/list s. description http://code.google.com/p/yaffs2utils/ ; extract to yaffs2util
* download snapshot as described in http://yaffs.net/node/346, extract the source file to directory yaffs2
* change ~/yaffs2util/Makefile with vi and set "KERNELDIR   = /usr/src/linux-headers-2.6.32-21" ; depending on the location of your header files
* execute make
* goto subfolder ~/yaffs2util/src and copy mkyaffs2 and unyaffs2 to /home/administrator/bin
* execute: PATH=$PATH:/home/administrator/bin
* 
```

### Links

[`androidoniphone.blogspot.com/2010/04/install-android-on-iphone-guide.html`](http://androidoniphone.blogspot.com/2010/04/install-android-on-iphone-guide.html) [`android-dls.com/wiki/index.php?title=Main_Page`](http://android-dls.com/wiki/index.php?title=Main_Page)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Applications and Services

> From: [eLinux.org](http://eLinux.org/Android_Applications "http://eLinux.org/Android_Applications")

# Android Applications

This page has some miscellaneous applications and services which may be of interest to developers:

## DLNA

A fast growing open-source stack for Android is

*   Cling - [`www.teleal.org/projects/cling/`](http://www.teleal.org/projects/cling/)

Alternatively you might want to look at

*   [`www.cybergarage.org/twiki/bin/view/Main/CyberLinkForJava`](http://www.cybergarage.org/twiki/bin/view/Main/CyberLinkForJava)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Android Derivatives

> From: [eLinux.org](http://eLinux.org/Android_Derivatives "http://eLinux.org/Android_Derivatives")

# Android Derivatives

This page has information about systems that are derived from Android.

## Why non-Google Android

*   [`www.reliableembeddedsystems.com/pdfs/2012-07-03-penguin-and-droid.pdf`](http://www.reliableembeddedsystems.com/pdfs/2012-07-03-penguin-and-droid.pdf)
    *   presentation by Robert Berger discussing non-Google Android markets

## Cyborgstack

This project aims to utilizes the Android system in other product categories, including deeply embedded:

*   web site: [`www.cyborgstack.org`](http://www.cyborgstack.org)
*   wiki: [`wiki.cyborgstack.org`](http://wiki.cyborgstack.org)
*   github: [`github.com/cyborgstack`](http://github.com/cyborgstack)
*   twitter: @cyborgstack
*   list: [`lists.cyborgstack.org/listinfo/dev`](http://lists.cyborgstack.org/listinfo/dev)

### Headless Android

From: [`lkml.org/lkml/2012/2/15/443`](https://lkml.org/lkml/2012/2/15/443)

As an FYI, I thought some of you might be interested in knowing that Android's user-space can be modified to run on headless systems (i.e. without a framebuffer.) IOW, you can configure the FB stuff completely out or have a kernel port that doesn't have an FB (yet?) and still run the Android use-space.

I've put this up as part of the Cyborgstack project:

```
$ repo init -u git://github.com/cyborgstack/android.git -b headless
$ repo sync
$ ... 
```

The relevant presentation from the Android Builders Summit is here: [cyborgstack-120213.pdf](http://eLinux.org/images/6/6f/Cyborgstack-120213.pdf "Cyborgstack-120213.pdf")

Essentially, Headless Android is the AOSP but WITHOUT:

*   SurfaceFlinger
*   WindowManager
*   WallpaperService
*   InputMethodManager

It gives you is all the Android framework but for ui-less systems (no FB.) What it means, is that, save for Activities, you can use the standard Android development tools (Eclipse, SDK/NDK, etc.) to create apps that use:

*   ContentProviders
*   Services
*   BroadcastReceivers

Why would you want this instead of using "Embedded Linux"? Honestly I was very skeptical when some developers first mentioned to me that they were interested in doing this. I was in fact very dismissive of it. But I kept getting more and more inquiries about this. So I decided to bite the bullet and give it a try.

Now that I have, I think there are 2 clear benefits to using this instead of "embedded Linux": 1) you get one platform for all your device development, whether it has a UI or not 2) your devices become programmable by any developer that knows the Android API (and, as you may know, there's growing number of those.)

That said, what I've done is very much a proof of concept. It's in fact a dirty hack at this point. Please don't ship this just yet. It needs a lot more eyeballs and certainly a lot more work. But, it's good enough to give you a taste of what's possible and allow you play with it.

Cheers,

-- Karim Yaghmour

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Linux emulators for Android

> From: [eLinux.org](http://eLinux.org/Linux_emulators_for_Android "http://eLinux.org/Linux_emulators_for_Android")

# Linux emulators for Android

## See also

*   [KBOX Linux emulator for Android](http://eLinux.org/KBOX_Linux_emulator_for_Android "KBOX Linux emulator for Android")

# Android 社区

Android 社区相关的新闻、事件、邮件列表、参与者以及面向 Android 开发人员的各类组织。

# News

> From: [eLinux.org](http://eLinux.org/Android_News "http://eLinux.org/Android_News")

# Android News

This page has links to news about the Android platform

## Contents

*   1 Android News Portals
*   2 Interviews
*   3 Industry/Consortium news
    *   3.1 Mentor Graphics acquires Embedded Alley
    *   3.2 OESF
*   4 Software
    *   4.1 Releases
    *   4.2 Ports
    *   4.3 Videos
*   5 Features
    *   5.1 Interfaces
    *   5.2 Applications
*   6 Phone news
*   7 Netbooks and MIDs

## Android News Portals

*   [`androidguys.com/`](http://androidguys.com/) - General news
*   [`androinica.com/`](http://androinica.com/) - Android Blog

## Interviews

*   [Google's Rubin: Android 'a revolution'](http://news.cnet.com/8301-1023_3-10245994-93.html) - CNet News May 2009

*"Android, Google's mobile operating system, doesn't generate revenue for the company, and likely never will--at least in the direct sense. But Andy Rubin, Google's director of mobile platforms, thinks Google and the world will benefit from any device created with the intent of getting more people onto the Internet, and isn't shy about explaining why the open-source approach chosen for Android holds the most promise of reaching that goal."*

## Industry/Consortium news

### Mentor Graphics acquires Embedded Alley

Mentor Graphics, a large EDA firm, acquires the embedded Linux consulting company Embedded Alley, in July, 2009.

*   See [`www.linuxfordevices.com/c/a/News/Mentor-Graphics-acquires-Embedded-Alley/`](http://www.linuxfordevices.com/c/a/News/Mentor-Graphics-acquires-Embedded-Alley/)

### OESF

The Open Embedded Software Foundation (OESF) is an Android Consortium formed in Japan in February, 2009\. It included 23 companies at startup, and has the support of Google.

*   See [`techon.nikkeibp.co.jp/english/NEWS_EN/20090325/167661/`](http://techon.nikkeibp.co.jp/english/NEWS_EN/20090325/167661/)

## Software

### Releases

*   [Android SDK version 3.0](http://developer.android.com/sdk/android-3.0.html) (Honeycomb)
*   [Android SDK version 2.3.3](http://developer.android.com/sdk/android-2.3.3.html)
*   [Android SDK version 2.3](http://developer.android.com/sdk/android-2.3.html) (Gingerbread)
*   [Android SDK version 2.2](http://developer.android.com/sdk/android-2.2.html) (Froyo)

*   [Android SDK version 2.1](http://developer.android.com/sdk/android-2.1.html) (Eclair 2.1) released (Jan 12, 2010)

    *   [`www.techtree.com/India/News/Android_21_SDK_Released/551-108663-580.html`](http://www.techtree.com/India/News/Android_21_SDK_Released/551-108663-580.html)
*   [Android version 1.6](http://developer.android.com/sdk/android-1.6.html) (Donut) released (version 4 released December 2009)

    *   [`developer.android.com/index.html`](http://developer.android.com/index.html)
    *   [`android-developers.blogspot.com/search/label/Android%201.6`](http://android-developers.blogspot.com/search/label/Android%201.6)
    *   [`developer.android.com/sdk/android-1.6-highlights.html`](http://developer.android.com/sdk/android-1.6-highlights.html)
*   [Android version 1.5](http://developer.android.com/sdk/android-1.5.html) (Cupcake) released (April 2009)

    *   [Android 1.5 'Cupcake' starts arriving on G1s](http://www.computerworld.com/action/article.do?command=viewArticleBasic&taxonomyName=mobile_devices&articleId=9132387&taxonomyId=75&intsrc=kc_top) ComputerWorld, April 30, 2009
    *   [Android 1.5 firmware available](http://lwn.net/Articles/330377/) LWN.net, April 27, 2009
        *   HTC (the manufacturer of the Android Dev Phone) has posted a set of Android 1.5 images for the ADP1\. There's also reasonably straightforward instructions on flashing those images into a phone

### Ports

*   [Android ported to MIPS](http://www.linuxdevices.com/news/NS2957041907.html) Linuxdevices, April 2009
    *   Embedded Alley has ported Android to the Alchemy MIPS processor.
*   [Android ported to various ARM processors](http://eLinux.org/Android_on_OMAP#Real_hardware "Android on OMAP") (ARM9, ARM11 and Cortex-A8 based). Including e.g. [OSK](http://eLinux.org/OSK "OSK") board, Nokia N800, Nokia N810, [BeagleBoard](http://eLinux.org/BeagleBoard "BeagleBoard"), [Devkit8000](http://eLinux.org/Devkit8000 "Devkit8000") and Gumstix Overo board.
*   [Devkit7000](http://www.armkits.com/product/devkit7000.asp) Samsung S5PV210 based board.
*   [Devkit8500D](http://www.armkits.com/product/devkit8500d.asp) TI DM3730 based board.

### Videos

*   [WLAN and Bluetooth demo on DM3730 running Android featured on You Tube](http://www.youtube.com/watch?v=kkhUqVVOpco)
*   [Android demo on DM3730 featured on You Tube](http://www.youtube.com/watch?v=QnP5f_jfqfE)
*   [Android Demo on Mistral's TMDSEVM3530 featured on You Tube](http://www.youtube.com/watch?v=3MKg8UAxgTE)
*   [Android Demo on Mistral's TMDXEVM3503 featured on You Tube](http://www.youtube.com/watch?v=IuO0YQfjflY)

## Features

### Interfaces

*   SenseUI (HTC)
*   MotoBlur (Motorola)
*   TouchWiz (Samsung)
*   Rachael (Sony Ericsson)

### Applications

*   Google Maps (with turn-by-turn navigation)
*   Google Goggles - search by image from camera
    *   [`www.pcmag.com/article2/0,2817,2356786,00.asp`](http://www.pcmag.com/article2/0,2817,2356786,00.asp)
        *   Hands off with Google Goggles
*   DoCoMo Augmented Reality Concept running on Android : [`www.akihabaranews.com/en/news-18527-DoCoMo+Augmented+Reality+Concept+at+Wireless+Japan+09.html`](http://www.akihabaranews.com/en/news-18527-DoCoMo+Augmented+Reality+Concept+at+Wireless+Japan+09.html)

## Phone news

*   Samsung phone
    *   [Samsung's Android phone to launch in Germany](http://www.linuxdevices.com/news/NS6111935151.html) Linuxdevices, April 2009
*   T-mobile ships more than 1 million G1 phones
    *   [T-Mobile sells a million Android phones](http://www.linuxdevices.com/news/NS9441485944.html) Linuxdevices, April 2009

## Netbooks and MIDs

*   [ARM-based MIDs run Android, report says](http://www.linuxdevices.com/news/NS2738659174.html) Linuxdevices, April 27, 2009
*   [First Android netbooks surface](http://www.linuxdevices.com/news/NS2416044211.html) Linuxdevices, April 2009
    *   ARM-based netbook by Skytone announced.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Events

> From: [eLinux.org](http://eLinux.org/Android_Events "http://eLinux.org/Android_Events")

# Android Events

The only events I know of (as of Jan 2010) with Android content are:

## Contents

*   1 Google Events
*   2 Android Builders Summit
*   3 Other Non-Google Events
*   4 Linux events
*   5 Regional events
*   6 Related Conferences
*   7 Previous Events
    *   7.1 Regional events

## Google Events

*   Google I/O - see the [Google I/O wikipedia entry](http://en.wikipedia.org/wiki/Google_I/O)
    *   Google I/O for 2011 was in on May 10,11 in San Francisco
        *   See [Tims GoogleIO 2011 Notes](http://eLinux.org/Tims_GoogleIO_2011_Notes "Tims GoogleIO 2011 Notes")
*   [Google Developer Days](http://www.google.com/events/developerday)
    *   Regional events with Google developers

## Android Builders Summit

This is a new event (in 2011) for Android systems developers. This includes developers who are porting Android to new hardware, working on the Android kernel or low-level infrastructure, or otherwise working on Android itself (not on Android application development).

*   [ABS home page](http://events.linuxfoundation.org/events/android-builders-summit/)
    *   [Presentation slides](http://events.linuxfoundation.org/events/android-builders-summit/slides)

## Other Non-Google Events

*   [Android Open Conference](http://androidopen.com/android2011/public/content/about)
    *   October 9-11, San Francisco
    *   This is an O'Reilly event, and appears to be trying to cover the whole Android ecosystem.
*   [AnDevCon II](http://www.andevcon.com/AnDevCon_II/index.html) - November 6-9, San Francisco
    *   Covers mostly application development, but has other sessions as well.

## Linux events

*   [`events.linuxfoundation.org/events/embedded-linux-conference/`](http://events.linuxfoundation.org/events/embedded-linux-conference/) Embedded Linux Conference

    *   See [`embeddedlinuxconference.com`](http://embeddedlinuxconference.com) for an overview of multiple years
        *   There were android talks especially at ELC Europe 2009 and 2010
        *   See Matt Porter's talk and Nina Wilner's talk at ELC Europe 2009 Presentations
        *   See Tim Bird's talk and the Android tutorial at ELC Europe 2010 Presentations
*   Some CELF Japan Jamborees have technical content related to Android

    *   The following had Android presentations: Japan Technical Jamboree 31, Japan Technical Jamboree 34

## Regional events

Upcoming...?

*   [Droidcon London 2010](http://www.droidcon.co.uk/) - October 28,29, 2010, London
*   [Droidcon Belgium](http://droidcon.be/) - next one = January 21, 2011, Brussels
*   [[`www.android-group.jp/abc2011w/`](http://www.android-group.jp/abc2011w/) Android Bazaar and Conference - Jan 9, 2011, Tokyo)

## Related Conferences

*   [Game Developer's conference](http://www.gdconf.com/) is starting to see Android participation
    *   See [`android-developers.blogspot.com/2010/01/android-at-2010-game-developers.html`](http://android-developers.blogspot.com/2010/01/android-at-2010-game-developers.html)

## Previous Events

*   [Android Developer's Conference](http://www.andevcon.com/) - March 7-9, 2011
    *   This seems to be the first full-blown Android developer event, covering mostly application development, but also some system talks
    *   archive online at???

### Regional events

*   [[`www.android-group.jp/abc2011w/`](http://www.android-group.jp/abc2011w/) Android Bazaar and Conference - Jan 9, 2011, Tokyo)
*   [Droidcon Belgium](http://droidcon.be/) - next one = January 21, 2011, Brussels
*   [Droidcon London 2010](http://www.droidcon.co.uk/) - October 28,29, 2010, London

[Categories](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")
*   [Events](http://eLinux.org/Category:Events "Category:Events")

# Web/Mailing List Directory

> From: [eLinux.org](http://eLinux.org/Android_Web_Resources "http://eLinux.org/Android_Web_Resources")

# Android Web Resources

## Contents

*   1 Web Sites
    *   1.1 Google sites
    *   1.2 Community sites
    *   1.3 Other sites
*   2 Mailing Lists
    *   2.1 Other lists

## Web Sites

### Google sites

*   [`developer.android.com/`](http://developer.android.com/) - site mainly for application developers
    *   [mailing lists for app developers](http://source.android.com/discuss#TOC-Android-application-developer-maili)
*   [`source.android.com/`](http://source.android.com/) - site devoted to source code management
    *   [mailing lists for source/platform discussions](http://source.android.com/discuss#TOC-Open-source-mailing-lists)

### Community sites

*   [Android-DLs](http://android-dls.com/)
    *   [Android-DLs wiki](http://android-dls.com/wiki/)
    *   [Android-DLs forum](http://android-dls.com/forum/)
*   xda-developers
    *   [XDA Developers Dream Android Development forum](http://forum.xda-developers.com/forumdisplay.php?f=448) - lots of good discussion about ROMS and Android system development
*   [AndroidCommunity.com](http://androidcommunity.com/)
*   Stack Overflow ([android-tagged questions](http://stackoverflow.com/questions/tagged/android))
*   [Android Freeware Lovers](http://www.freewarelovers.com/android) - community driven repo of open source and freeware apps
*   [Android Freeware](http://freeapk.com) Android freeware or on sales apps

### Other sites

*   [Android™ on OMAP](http://www.mistralsolutions.com/android)
*   [Android Demo on Mistral's TMDSEVM3530 featured on You Tube](http://www.youtube.com/watch?v=3MKg8UAxgTE)
*   [Android Demo on Mistral's TMDXEVM3503 featured on You Tube](http://www.youtube.com/watch?v=IuO0YQfjflY)

## Mailing Lists

See [`developer.android.com/community/index.html`](http://developer.android.com/community/index.html) for a list of official Android mailing lists, and instructions for joining these lists.

*   [android-platform](http://groups.google.com/group/android-platform)
    *   Discussion of user-space Android code, including system libraries, service, public APIs or built-in applications.
*   [android-porting](http://groups.google.com/group/android-porting)
    *   Toolchains, device-specific customizations, board bring-up and optimizations
*   [android-kernel](http://groups.google.com/group/android-kernel)
    *   Discussions of Linux kernel and Android-related changes
*   [repo-discuss](http://groups.google.com/group/repo-discuss) - Repo and Gerrit discussion
*   [android-beginners](http://groups.google.com/group/android-beginners)
    *   Beginning users of SDK and getting started with Android development
*   [android-developers](http://groups.google.com/group/android-developers)
    *   Experienced developers - implementation help, optimizations, etc.
*   [android-discuss](http://groups.google.com/group/android-discuss)
    *   General discussion of the platform, usage, ideas for new features, etc.
*   [android-security-discuss](http://groups.google.com/group/android-security-discuss)
    *   Security issues. Please don't disclose vulnerabilities directly on this list.
*   [android-security-announce](http://groups.google.com/group/android-security-announce)

    *   Low-volume list for security announcements by the Android Security Team.
*   [Android Market Help Forum](http://www.google.com/support/forum/p/Android+Market?hl=en)

    *   A web-based discussion forum for Android Market questions.

### Other lists

*   [android-ndk](http://groups.google.com/group/android-ndk) - Android Native Development Kit (NDK) mailing list
    *   List for people working on native code. This list is targeted by Google at supporting developers who are writing native shared libraries to use via JNI from their Java-based applications. You won't get any help using 'agcc' or other community-developed native development tools there.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# People

> From: [eLinux.org](http://eLinux.org/Android_People "http://eLinux.org/Android_People")

# Android People

Here are some of the key Android developers you may encounter on mailing lists or at events:

## Google People

See [Google's listing of the Android core technical team](http://source.android.com/project/core-technical-team)

*   [Dianne Hackborn](http://www.angryredplanet.com/~hackbod/) - platform programmer, worked on original OpenBinder
*   Brian Swetland - lead kernel developer
    *   See [`lkml.org/lkml/2009/6/10/397`](http://lkml.org/lkml/2009/6/10/397)
*   Arve Hjønnevåg - kernel developer (original submitter of wakelock patches)
*   Mike Chan - Droid developer
*   Dan Bornstein - Dalvik creator
*   Chris DiBona - Open Source Programs Manager at Google

## Non-google People

*   Karim Yaghmour - President of [Opersys](http://www.opersys.com), long-time Embedded Linux and Android expert, author and trainer
*   Greg Kroah-Hartman - long-time kernel developer, sometimes responds on android-kernel mailing list
*   [Tim Riker](http://rikers.org/) [linked-in account](http://www.linkedin.com/in/timriker) - CTO at [Saygus](http://saygus.com/), maker of an Android-based video phone
*   Tetsuyuki Kobayashi - Developer at Kyoto MicroComputer. Has an excellent blog with several great articles about Android. [`kobablog.wordpress.com/`](http://kobablog.wordpress.com/)
    *   presentation: [Android is not just Java on Linux](http://kobablog.wordpress.com/2011/05/22/android-is-not-just-java-on-linux/)
        *   an excellent overview of Android
    *   page of presentations by Kobayashi-san: [`www.slideshare.net/tetsu.koba/presentations`](http://www.slideshare.net/tetsu.koba/presentations)

### Hacker community

*   Steve Kondik (also known as Cyanogen) - creator and maintainer of the [cyanogenmod](http://www.cyanogenmod.com/) custom images
*   Benno - has a blog, provides busybox binary (V.P. of Engineering at Open Kernel Labs)
    *   [Android Framework Startup](http://benno.id.au/blog/2007/11/18/android-framework-startup) tutorial on framework startup (and how to change a ramdisk.img)
    *   [Android - under the hood](http://benno.id.au/blog/2007/11/13/android-under-the-hood) analysis of system startup
    *   [list of android articles by Benno](http://benno.id.au/blog/?tag=android)
    *   [busybox binary](http://benno.id.au/blog/2007/11/14/android-busybox)
*   JesusFreke - Used to make custom system images. Author of smali/baksmali (dex assembler/disassembler)
    *   [JesusFreke's Blog](http://jf.andblogs.net/) (with release anouncements)
    *   [smali project page](http://smali.googlecode.com)
*   Haykuro - makes custom system images
    *   [Haykuro's site](http://haykuro.theiphoneproject.org/) (blog, info)
*   Brian Gupta (aka Brandorr) - author of FAQ at Android-DLs
*   TheDudeOfLife - makes custom system images
    *   [announcement of 1.5 (cupcake) build](http://forum.xda-developers.com/showthread.php?t=507151)
*   Others on xda-developers: xmoo, Stericson, dapro, marcusmaximus, manup456

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")

# Organizations

> From: [eLinux.org](http://eLinux.org/Android_Organizations "http://eLinux.org/Android_Organizations")

# Android Organizations

Here are some Android organizations

*   [Open Handset Alliance](http://www.openhandsetalliance.com/) - the original group of companies organized by Google to collaborate on Android technologies and delivery

## Regional

*   [Korean Android Platform Group](http://www.kapg.org/)

    *   Have published (or are endorsing) a book: [Inside Android](http://www.kapg.org/E_Inside_Android)
*   [Android Association of Japan](http://www.android-group.jp/) (or Japan Android Group)

*   [`www.yokohama.android-pf.org/`](http://www.yokohama.android-pf.org/) Yokohama Android Platform Club

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Android](http://eLinux.org/Category:Android "Category:Android")