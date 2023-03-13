---
title: "Optimizing Binaries - App, Archive, XCFramework, Binary anatomy"
date: 2023-01-07T15:06:27-05:00
draft: true
---

In order to reduce your app size, you have to first understand the internal structure of what you're trying shrink. I recall when I when first tacked all this I couldn't tell the difference between an XCFramework vs a binary. Nor I knew how to create an archive. Understanding all of them get better/quicker visibility into your build artifacts at different stages. 

To understand why XCFrameworks were created and what problem they intend to solve and its difference with FAT binaries see [here](https://stackoverflow.com/a/75454378/5175709)
As for the high level steps what you end up doing with your app is: 

## High Level 
1. You build your app.
2. You create an archive of that build. Much like a compressed version of your build. Some stripping may happen in this phase. 
3. You create an ipa for each different device model
4. You distribute your ipa to the App Store
5. App Store applies some final touches to the build. This affects the app size. 

## Low Level

Not everything you build makes its way into the archive. This is why you have to mark your top level deliverables with `SKIP_INSTALL=NO`. It means keep them in the archive. For more on that see [here](https://stackoverflow.com/questions/16374851/xcode-4-target-build-setting-skip-install-what-is-it)

### XCArchive

An XCArchive is an archive of a product built from Xcode. It could be an iOS app, or a Mac app, or anything else. It also contains a number build results that aren’t included in an IPA, including (but not limited to) symbols.
From an archive, you can then repeatably produce several things depending on the product — something suitable for distribution to an iPhone, or the App Store, or if you’re building a Mac app you can create notarized distributions of the app. If you’re making frameworks you can produce an .xcframework and a documentation package, etc etc etc. Notice not all of those use cases involve IPAs.


You archive your app. This creates an XCArchive. (The prefix XC usually means some involvement for Xcode. Like XCBuild, xcodebuild, XCFrameworks and so on.)

[Reducing your app’s size](https://developer.apple.com/documentation/xcode/reducing-your-app-s-size)

Before you start optimizing your app, you first need to measure its download and installation sizes. However, none of the binaries that you create for debugging or that you upload to the App Store from within Xcode are suitable for measuring your app’s size. For example, you can’t use any of the following binaries:
The APP file (the app bundle)
The XCARCHIVE bundle that you create when your archive your app
The IPA file that you upload to App Store Connect
Those binaries contain resources and files that aren’t part of the bundles that your users download from the App Store; for example, DSYM files for crash reporting.

Apps distributed for testing with TestFlight contain additional data that an App Store build doesn’t have, so the TestFlight build is larger. This additional data isn’t included in your app when you make it available in the App Store. However, when compared to the binary you uploaded, the final size of your app after it’s approved for the App Store may end up being slightly larger. This size increase can happen when the App Store performs additional processing on your app’s binaries, adding DRM to prevent app piracy and then re-compressing the binaries.

## Automate the generation of the app size report
Instead of creating the app size report with Xcode, you may want to automate its generation in build scripts or continuous integration workflows. Run the following command to use xcodebuild to export your app for distribution and create an app thinning size report:

```sh
xcodebuild -exportArchive -archivePath iOSApp.xcarchive -exportPath Release/MyApp -exportOptionsPlist OptionsPlist.plist
```

Replace all file names and paths as necessary. To create the app size report, provide xcodebuild with an export options property list. Be sure to include the thinning key, with <thin-for-all-variants> as the value.


[About -exportOptionsPlist](https://www.matrixprojects.net/p/xcodebuild-export-options-plist/)

👆 Best link 👆

https://www.runtastic.com/blog/en/frameworks-ios/

[Reduce the size of your app by stripping Swift symbols](https://help.apple.com/xcode/mac/current/#/dev6301c07c6)

Why do I want Swift symbols stripped from my app?
In most cases the symbols are not required for Swift frameworks including the Swift standard library. Stripping out the symbols can make apps significantly smaller.
How much space does symbol stripping save?
The amount of space you will save depends on a few factors. To find the size difference, build a distribution version of your app with and without the symbols stripped, install each of them on a device, and compare the size. You can build a distribution version for a registered device, for manual distribution, or for TestFlight. Looking at the size of an archive build is not a reliable way to measure the size of an iOS, watchOS or tvOS app because the Swift standard libraries are only stripped as part of App Thinning.

[What is an XCFramework?](https://help.apple.com/xcode/mac/current/#/dev6f6ac218b)
An XCFramework is a distributable binary package created by Xcode that contains variants of a framework or library so that it can be used on multiple platforms (iOS, macOS, tvOS, and watchOS), including Simulator builds. An XCFramework can be either static or dynamic and can include headers.

To use a prebuilt XCFramework, link the target to the XCFramework. Xcode ensures that the target can build against the XCFramework’s headers, link against its binary, and embed it for distribution. If your app has multiple targets (such as app extensions) that use the same XCFramework, you should pick one target (usually your app’s target) to embed the XCFramework and the others should link it without embedding.

[Create an XCFramework](https://help.apple.com/xcode/mac/current/#/dev544efab96)

[Monitor the status of builds](https://help.apple.com/xcode/mac/current/#/devb7cf06445)
EXCELLENT IMAGE

[object Object]
https://www.cocoanetics.com/2015/04/skipping-copy-phase-strip/

FANTASTIC links
https://theswiftdev.com/the-swift-compiler-for-beginners/
https://fuchsia.googlesource.com/third_party/swift/+/refs/tags/swift-DEVELOPMENT-SNAPSHOT-2018-01-17-a/docs/Driver.md

Just need to figure the .o vs IL vs assembly. 