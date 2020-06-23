## Why another Superuser?
* Superuser should be open source. It's the gateway to root on your device. It must be open for independent security analysis. Obscurity (closed source) is not security.
* Superuser should be NDK buildable. No internal Android references.
* Superuser should also be AOSP buildable for those that want to embed it in their ROM.
* Superuser should also be AOSP _embeddable_, meaning a ROM can easily embed it into their Settings app.
* Maintenance and updates on both the market and source repositories should be timely.
* I want to be able to point users of my app to a Superuser solution that I wrote, that I know works, and that I can fix if something is wrong.
* Handle multiuser (4.2+) properly
* Handle concurrent su requests properly

## Checking out the source

~~You'll need the "Widgets" dependency.~~
Widgets dependency included into project now.

* $ mkdir /path/to/src
* $ cd /path/to/src
* $ git clone git://github.com/koush/Superuser
* ~~$ git clone git://github.com/koush/Widgets~~

These repositories do not keep the actual projects in the top level directory.
This is because they contain tests, libs, and samples.

## Before starting to compile

Android team has decided to remove all old scripts (including ant) from SDK in march release of 25.3.0 SDK Tools. You can use old SDK Tools to compile with ant.

Old SDK Tools releases:
* [Linux](https://dl.google.com/android/repository/tools_r25.2.5-linux.zip)
* [MacOS](https://dl.google.com/android/repository/tools_r25.2.5-macosx.zip)
* [Windows](https://dl.google.com/android/repository/tools_r25.2.5-windows.zip)

For more information, [click here](https://stackoverflow.com/a/42921645)

## Eclipse

In Eclipse, import ~~Widgets/Widgets and~~ Superuser/Superuser. It should Just Work (TM).

## Ant

* $ mkdir /path/to/src
* $ cd /path/to/src
* $ cd Superuser/Superuser

In this directory, create a file called local.properties. This file is used by ant for custom properties. You need to specify the location of the ndk directory:

```
ndk.dir=/Users/koush/src/android-ndk
```

Then you can build:

* $ ant release

Outputs:
* bin/update.zip - Recovery installable zip
* bin/Superuser.apk - Superuser Android app
* libs/armeabi/su - ARM su binary
* libs/x86/su - x86 su binary

## Building the su binary

You can use ant as shown above, to build the binary, but it can also be built without building the APK.

Make sure you have the android-ndk downloaded with the tool "ndk-build" in your path.

* $ cd /path/to/src/
* $ cd Superuser/Superuser
* $ ndk-build

The su binary will built into Superuser/Superuser/libs/armeabi/su.



## Building with AOSP, CyanogenMod, etc

ROM developers are welcome to distribute the official Superuser APK and binary that I publish. That will
allow them to receive updates with Google Play. However, you can also build Superuser as part of your
build, if you choose to.

There are two ways to include Superuser in your build. The easiest is to build the APK as a separate app.
To do that, simply add the local_manifest.xml as described below. The second way is by embedding it
into the native Android System Settings.

#### Repo Setup
Add the [local_manifest.xml](https://github.com/koush/Superuser/blob/master/local_manifest.xml) to your .repo/local_manifests

#### Configuring the Package Name
The Superuser distributed on Google Play is in the package name com.koushikdutta.superuser.
To prevent conflicts with the Play store version, the build process changes the package
name to com.thirdparty.superuser. You can configure this value by setting the following
in your vendor makefile or BoardConfig:

```
SUPERUSER_PACKAGE := com.mypackagename.superuser
```

#### Advanced - Embedding Superuser into System Settings

You will not need to change the package name as described above. Superuser will simply go
into the com.android.settings package.

First, in a product makefile (like vendor/cm/config/common.mk), specify the following:

```
SUPERUSER_EMBEDDED := true
```

To modify packages/apps/Settings, you will need this [patch](http://review.cyanogenmod.org/#/c/32957/2//COMMIT_MSG,unified).
The patch simply references the sources checked out to external/koush and makes changes
to XML preference files and the AndroidManifest.xml. It is a very minimal change.

