---
title: "linking object files using ld"
date: 2023-05-08T21:49:50-04:00
draft: true
---

I have two swift files. main.swift and other.swift.
I compile them as such

```
swiftc -emit-object main.swift other.swift Greeter
```

When I do: 

```
clang -o Greet main.o other.o
```

I get: 

```
ld: warning: Could not find or use auto-linked library 'swiftFoundation'
ld: warning: Could not find or use auto-linked library 'swiftDarwin'
ld: warning: Could not find or use auto-linked library 'swiftCore'
ld: warning: Could not find or use auto-linked library 'swiftCoreGraphics'
ld: warning: Could not find or use auto-linked library 'swift_Concurrency'
ld: warning: Could not find or use auto-linked library 'swiftDispatch'
ld: warning: Could not find or use auto-linked library 'swiftXPC'
ld: warning: Could not find or use auto-linked library 'swiftCoreFoundation'
ld: warning: Could not find or use auto-linked library 'swiftIOKit'
ld: warning: Could not find or use auto-linked library 'swiftSwiftOnoneSupport'
ld: warning: Could not find or use auto-linked library 'swiftObjectiveC'
Undefined symbols for architecture x86_64:
  "Swift.String.init(stringInterpolation: Swift.DefaultStringInterpolation) -> Swift.String", referenced from:
      _main in main.o
  "Swift.String.init(_builtinStringLiteral: Builtin.RawPointer, utf8CodeUnitCount: Builtin.Word, isASCII: Builtin.Int1) -> Swift.String", referenced from:

<removed some messages for brevity>
ld: symbol(s) not found for architecture x86_64
clang: error: linker command failed with exit code 1 
```

Solution
-

Pass `-v` to `swiftc`, to see verbose mode and then try to exactly follow its form. 

Details
- 

    swiftc main.swift other.swift Greeter -v

Output: 

```
Apple Swift version 5.6 (swiftlang-5.6.0.323.62 clang-1316.0.20.8)
Target: x86_64-apple-macosx12.0
/Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-frontend -frontend -c -primary-file main.swift other.swift -target x86_64-apple-macosx12.0 -enable-objc-interop -stack-check -sdk /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk -color-diagnostics -new-driver-path /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-driver -resource-dir /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift -module-name main -target-sdk-version 12.3 -o /var/folders/gp/q_3k63zj46g24mq0t2tz_s640000gp/T/TemporaryDirectory.v9ZEgV/main-1.o
/Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-frontend -frontend -c main.swift -primary-file other.swift -target x86_64-apple-macosx12.0 -enable-objc-interop -stack-check -sdk /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk -color-diagnostics -new-driver-path /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-driver -resource-dir /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift -module-name main -target-sdk-version 12.3 -o /var/folders/gp/q_3k63zj46g24mq0t2tz_s640000gp/T/TemporaryDirectory.v9ZEgV/other-1.o
/Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld /var/folders/gp/q_3k63zj46g24mq0t2tz_s640000gp/T/TemporaryDirectory.v9ZEgV/main-1.o /var/folders/gp/q_3k63zj46g24mq0t2tz_s640000gp/T/TemporaryDirectory.v9ZEgV/other-1.o /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/clang/lib/darwin/libclang_rt.osx.a -syslibroot /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk -lobjc -lSystem -arch x86_64 -L /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/macosx -L /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk/usr/lib/swift -platform_version macos 12.0.0 12.3.0 -o main
```

invoking that command uses `swift-frontend` and `ld` from with your Xcode's command line tools

`swift-frontend` is invoked from here:

```
/Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/swift-frontend
```

`ld` is invoked from here: 
```
/Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin/ld
```


I then followed an identical pattern to invoke `ld` myself. It worked 🎉.


ORIGINAL


```
ld main.o other.o \
/Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/clang/lib/darwin/libclang_rt.osx.a \
-syslibroot /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk \
-lobjc -lSystem -arch x86_64 -L /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/macosx \
-L /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk/usr/lib/swift \
-platform_version macos 12.0.0 12.3.0 -o main
```


NOTES
```
ld main.o other.o \
/Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/clang/lib/darwin/libclang_rt.osx.a \ (NOT REQUIRED)
-syslibroot /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk \ (REQUIRED otherwise -lobjc & -lSystem fail. They're the folder's /usr/lib)
-lobjc -lSystem -arch x86_64 -L /Applications/Xcode13.3.1.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/lib/swift/macosx \ (NOT REQUIRED)
-L /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk/usr/lib/swift \ (REQUIRED missing stuff like swiftFoundation swiftDarwin...)
-platform_version macos 12.0.0 12.3.0 -o main
```

Matches the requirements from doing `otool -L main`
```
~/Dev/Greeter (main) $ otool -L main 
main:
	/usr/lib/libobjc.A.dylib (compatibility version 1.0.0, current version 228.0.0)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1311.100.3)
	/usr/lib/swift/libswiftCore.dylib (compatibility version 1.0.0, current version 5.6.0)
	/usr/lib/swift/libswiftCoreFoundation.dylib (compatibility version 1.0.0, current version 14.0.0, weak)
	/usr/lib/swift/libswiftCoreGraphics.dylib (compatibility version 1.0.0, current version 2.0.0, weak)
	/usr/lib/swift/libswiftDarwin.dylib (compatibility version 1.0.0, current version 0.0.0, weak)
	/usr/lib/swift/libswiftDispatch.dylib (compatibility version 1.0.0, current version 11.0.0, weak)
	/usr/lib/swift/libswiftFoundation.dylib (compatibility version 1.0.0, current version 72.105.0, weak)
	/usr/lib/swift/libswiftIOKit.dylib (compatibility version 1.0.0, current version 1.0.0, weak)
	/usr/lib/swift/libswiftObjectiveC.dylib (compatibility version 1.0.0, current version 3.0.0, weak)
	/usr/lib/swift/libswiftXPC.dylib (compatibility version 1.0.0, current version 1.1.0, weak)
```


Findings
- 

1. This is why every Xcode can compile things differently. It's because they have most of their toolchain within themselves. Both `swift-frontend` and `ld` are from Xcode. Even if they exist on my os, they don't get used. This way Xcode's delivery system and requirements are less tied to an OS version. But this also leads to a bloated Xcode install.
1.1. When `ld` does `-syslibroot /Applications/Xcode13.3.1.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX12.3.sdk \` this is because you want it to be linked against for a specific env
2. To compile the most vanilla swift file, you need to link with a number of system libraries. 
3. To like to a library, you can't just give a directory, you also need to mention the specific library names as well. That's why if I remove `-lobjc` or `-lSystem` you'd start to linker errors. 

