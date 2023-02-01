---
title: "Why is xcframework causing extra size"
date: 2022-04-23T04:17:58-04:00
category: 'xcframework-extra-size'
draft: true
---
create a base branch off of main. feature/xcf/base
Find out the current DHGatewayOnboarding that’s pointing to 27.10.0


main 
xcframework with Karen’s original instructions. Zero changes
xcframework along with Build setting changes. 

Don’t Forget
log showbuildSettings
save the entire build log for apple developers to do an xcarchive
 The default optimization level for the Release configuration is Fastest, Smallest [-Os]

XCFramework. No changes
document the commands we used
create a branch for dh-gateway: xcframework-nochanges/27
create a branch for its dh-ios
make an xcarchive
make the ipas
store it

XCFramework. With changes
document the commands we used
strip debug symbols. 
Think if there’s anything else to change
create a branch for dh-gateway: xcframework-withchanges/27
create a branch for its dh-ios: xcframework-withchanges/27
make an xcarchive
make the ipas
store it

Other things to consider
we’re pulling in test files for our XCFramework. 

References 

Reducing your app’s size

Before you start optimizing your app, you first need to measure its download and installation sizes. However, none of the binaries that you create for debugging or that you upload to the App Store from within Xcode are suitable for measuring your app’s size. For example, you can’t use any of the following binaries:
The APP file (the app bundle)
The XCARCHIVE bundle that you create when your archive your app
The IPA file that you upload to App Store Connect
Those binaries contain resources and files that aren’t part of the bundles that your users download from the App Store; for example, DSYM files for crash reporting.

Apps distributed for testing with TestFlight contain additional data that an App Store build doesn’t have, so the TestFlight build is larger. This additional data isn’t included in your app when you make it available in the App Store. However, when compared to the binary you uploaded, the final size of your app after it’s approved for the App Store may end up being slightly larger. This size increase can happen when the App Store performs additional processing on your app’s binaries, adding DRM to prevent app piracy and then re-compressing the binaries.

Automate the generation of the app size report
Instead of creating the app size report with Xcode, you may want to automate its generation in build scripts or continuous integration workflows. Run the following command to use xcodebuild to export your app for distribution and create an app thinning size report:
xcodebuild -exportArchive -archivePath iOSApp.xcarchive -exportPath Release/MyApp -exportOptionsPlist OptionsPlist.plist
For the plist thingi see here

:point_up_2:Best link :point_up_2:
https://www.matrixprojects.net/p/xcodebuild-export-options-plist/

Reduce the size of your app by stripping Swift symbols
Why do I want Swift symbols stripped from my app?
In most cases the symbols are not required for Swift frameworks including the Swift standard library. Stripping out the symbols can make apps significantly smaller.
How much space does symbol stripping save?
The amount of space you will save depends on a few factors. To find the size difference, build a distribution version of your app with and without the symbols stripped, install each of them on a device, and compare the size. You can build a distribution version for a registered device, for manual distribution, or for TestFlight. Looking at the size of an archive build is not a reliable way to measure the size of an iOS, watchOS or tvOS app because the Swift standard libraries are only stripped as part of App Thinning.

What is an XCFramework?
An XCFramework is a distributable binary package created by Xcode that contains variants of a framework or library so that it can be used on multiple platforms (iOS, macOS, tvOS, and watchOS), including Simulator builds. An XCFramework can be either static or dynamic and can include headers.

To use a prebuilt XCFramework, link the target to the XCFramework. Xcode ensures that the target can build against the XCFramework’s headers, link against its binary, and embed it for distribution. If your app has multiple targets (such as app extensions) that use the same XCFramework, you should pick one target (usually your app’s target) to embed the XCFramework and the others should link it without embedding.

Create an XCFramework

Monitor the status of builds
EXCELLENT IMAGE

[object Object]
https://www.cocoanetics.com/2015/04/skipping-copy-phase-strip/

Open Question: 

What exactly are these flags? 

**Debug Information Format (level of detail)**
`DEBUG_INFORMATION_FORMAT`
The type of debug information to produce. * DWARF: Object files and linked products will use DWARF as the debug information format. [dwarf] * DWARF with dSYM File: Object files and linked products will use DWARF as the debug information format, and Xcode will also produce a dSYM file containing the debug information from the individual object files (except that a dSYM file is not needed and will not be created for static library or object file products). [dwarf-with-dsym]

Project: DWARF with dSYM File; 
    Target - Debug: DWARF
    Target - Release: DWARF with dSYM File


Strip Debug Symbols During Copy (
`COPY_PHASE_STRIP`
Specifies whether binary files that are copied during the build, such as in a Copy Bundle Resources [any non-swift file. Xib, Storyboard, json, plist, otf, strings ] or [Copy Files](https://developer.apple.com/documentation/xcode/customizing-the-build-phases-of-a-target#Copy-Files-to-the-Finished-Product) [This **isn't** the Compile (Swift) Sources. Most likely you don't have. ] build phase, should be stripped of debugging symbols. It does not cause the linked product of a target to be stripped—use `STRIP_INSTALLED_PRODUCT` for that.

Project: Yes
Target: No

Generate Debug Symbols (decides if you should generate symbols)
`GCC_GENERATE_DEBUGGING_SYMBOLS`
Enables or disables generation of debug symbols. When debug symbols are enabled, the level of detail can be controlled by the DEBUG_INFORMATION_FORMAT setting.

Project & Target: YES

Strip Linked Product
`STRIP_INSTALLED_PRODUCT`
If enabled, the linked product of the build will be stripped of symbols when performing deployment postprocessing.

Project & Target: YES


Deployment Postprocessing
`DEPLOYMENT_POSTPROCESSING`
If enabled, indicates that binaries should be stripped and file mode, owner, and group information should be set to standard values.

Project & Target: NO
See: https://github.com/Carthage/Carthage/issues/2485


Strip Style
`STRIP_STYLE`
The level of symbol stripping to be performed on the linked product of the build. The default value is defined by the target’s product type. * All Symbols: Completely strips the binary, removing the symbol table and relocation information. [all, -s] * Non-Global Symbols: Strips non-global symbols, but saves external symbols. [non-global, -x] * Debugging Symbols: Strips debugging symbols, but saves local and global symbols. [debugging, -S]

Project & Target: ALL SYMBOLS

Which project should we use? 

Should we generate the XCFramework using the Pods project and the library’s scheme? Or the example project and the example’s scheme?!


Maybe look into the log diff between cocoapods compilation vs xcframework compilation logs. Honestly not sure how to do that. 

## CocoaPods

About CocoaPods `post_install` hook, it's not something that happens during the Build Phase i.e. it doesn't get executed upon every run. It gets executed upon doing `pod install` i.e. once you do `pod install` your project setting, or whatever that you're changing within the `post_install` hook will get changed. And remain changed until you do sth else to change it.

When installing a dependency using CocoaPods, what ends up happening is that, CocoaPods doesn't pull in your dependency as and all the other dependencies of the pod as framework. 


It goes like I need Foo pod. Ok. Foo depends on Bar and Baz. Ok Let's install them too. 
Then it creates a framework for each of them. And now Foo can live. 

Does Pod's project setting matter? Or its' all the Pods project in the host app that matters? I suppose when it's a binary/xcframework then it will refer to the settings that were used in the Pod project itself when it creates the binary/xcframework. 

Probably cause the `ENABLE_BITCODE` setting was used **when building the framework**

That is, the pod, if turned into an xcframework, then it will inherit whatever build settings that is applied to the Pod (not the Pods project in the host app that pulls it. The project of the pod itself) project itself.

However if I don’t turn it into a xcframework, then the build settings is to be determined later, i.e. within the Pods project (of the main app) that converts the source code into a framework.
And because I have different

## Embedding

The linker can cause embedding of things. It won't do it always though. 

A static linked library, will already be within the binary. Makes no need for embedding. 

A dynamic linked library, will not be in the binary of the main app. Hence it needs be embedded. I think 


## dSYM
Compiled code usually contains debug information. It's _upon_ compile where you create the dSYM. This means dSYMs have to be created when you compile. 

Other References
https://www.bugsee.com/blog/ios-crash-symbolication-dummies-part-2/
https://github.com/CocoaPods/CocoaPods/issues/10277
https://chromium.googlesource.com/external/webrtc/+/06f7e49438dca7446db02daeaa596772e4be20f8/webrtc/build/ios/SDK/README
https://github.com/home-assistant/iOS/pull/2234
https://stackoverflow.com/a/62219237
https://stackoverflow.com/a/69163719
https://developer.apple.com/videos/play/wwdc2021/10211/
https://developer.apple.com/forums/thread/663920
https://medium.com/macoclock/how-to-build-and-use-an-xcframework-40e4e7a53e2c (not imporant)


## dyld vs ld
https://www.ibm.com/docs/en/aix/7.2?topic=techniques-when-use-dynamic-linking-static-linking (EXCEPTIONAL)
https://holyswift.app/frameworks-embed-or-not-embed-thats-the-question/

## To show Apple your Build Settings do: 

just do sth like this: 

```bash
xcodebuild archive -project "Example/Pods/Pods.xcodeproj" -scheme "DHGatewayOnboarding (Pods project)" -destination "generic/platform=iOS Simulator" -configuration Release only_active_arch=no -archivePath "build/test/iOS-Sim" SKIP_INSTALL=NO BUILD_LIBRARY_FOR_DISTRIBUTION=YES OTHER_SWIFT_FLAGS="-Xfrontend -module-interface-preserve-types-as-written" -showBuildSettings   
```

The output will reflect a **MERGE & OVERRIDE** the flags passed. The value coming from the command line will override the value coming from Xcodebuild settings. 

## how to look into a binary and see its symbols
https://bdunagan.com/2010/05/15/symbolification-shipping-symbols/

## xcworkspace has schemes
has schemes that you don't see. 

if you looko into the workproject they're not visible. 

Running `xcodebuild -list` in the following directories will have different results: 
- Root Directory where `xcworkspace` is present
- Example Directory where `FooPodName.xcodeproj` is present
- Pods Directory where `Pods.xcodeproj` is present

Most of the time you want to build things using the a scheme that's tied to the workspace. Not the project.

## Stripping 
https://en.wikipedia.org/wiki/Symbol_table
https://bdunagan.com/2010/05/15/symbolification-shipping-symbols/
MUST SEE: https://github.com/Carthage/Carthage/issues/2485
MUST SEE: https://docs.emergetools.com/docs/strip-binary-symbols

## TODO: 
look into sybmolification, symbol, debug symbols from https://help.apple.com/xcode/mac/current/
https://developer.apple.com/videos/play/wwdc2018/415/
https://developer.apple.com/videos/play/wwdc2021/10210/
https://wwdctogether.com/wwdc2016/413
https://developer.apple.com/forums/thread/715385 (More or less where I am myself + some more tips). See the symbol talk...
https://homepage.cs.uiowa.edu/~dwjones/cross/smal32/external.html
https://www3.physnet.uni-hamburg.de/physnet/Tru64-Unix/HTML/APS31DTE/DOCU_014.HTM
https://learn.microsoft.com/en-us/cpp/error-messages/tool-errors/linker-tools-error-lnk2001?view=msvc-170
https://www.symbolcrash.com/2019/02/25/so-you-want-to-be-a-mach-o-man/
https://lowlevelbits.org/parsing-mach-o-files/
https://developer.apple.com/library/archive/documentation/Performance/Conceptual/CodeFootprint/Articles/MachOOverview.html
https://developer.apple.com/videos/play/wwdc2022/110370
https://github.com/lu15gv & https://www.youtube.com/watch?v=lGG0UPdvc54

https://www.emergetools.com/blog/posts/how-xcode14-unintentionally-increases-app-size (Fantasic link!)
## Manual Framework creation
1. In a project. You can do Add File. Then add an `xcodeproj` file i.e. it doesn't have to be just swift file. You can add an entire xcodeproj file itself!

2. When creating a project, if you have another project open (or maybe it's based on directory. Not sure), you're given the option to just add it under...