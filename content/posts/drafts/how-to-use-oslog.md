---
title: "How to Use OSLog?"
date: 2022-01-17T22:39:20-05:00
draft: true
---
xxxxxxy
Out of the box with iOS, you get `print`. In Objective-C world, you'd get `NSLog`. `print` has a very limited API. `NSLog` is clunky and old. Apple introduced us the `os.log` framework. It's a powerful yet simple to use API. This post assumes you're targeting iOS15 users only. But before we begin you might ask why should I care about `OSLog`. [Quoting Apple](https://developer.apple.com/documentation/os/logging): 

> - When you are unable to attach a debugger to the app, such as when you’re diagnosing problems on a user’s machine.
> - When the problem is intermittent, and is difficult to catch in the debugger.
> - When you want to get a general sense of your app’s behavior—for example, you want to know when certain tasks start and end.

Additional reasons I can think of are: 

- We once had an issue that only appeared upon app startup from a push notification. Our own logs had to be queried from online. But that was slow and we didn't have the nice GUI of console. 
- Connecting the debugger to an app extension target is often a buggy experience. It's gets more messy when you want to debug the app extension and host app together. Using `os.log` just seems more natural. 
- Debugging an app launch. Often connecting it to the debugger is often slow. Beyond that connecting to Xcode creates an unreal experience. That is, Xcode is greedy and will keep the app in memory, for continous debugging. For real users that's not the case i.e. based on the context, the OS will kill or suspend the app. 
- You want to see something in realtime. However often the logging mechanism/server you use might have 2-3 minute latency or often they won't trigger until the next app launch...
- You wanna see the system logs tandem with your own logs. Example you what logs the OS produced in the context of rendering an image or in the context of networking or bluetooth. 
- You might wanna show these logs to an Apple Engineer as well. For more on that see [my discussion with an Apple Engineer]() 
- It's super fast and requires no integration. The logging is done in an _asynchronous_ way. For more on that see [here]()

All of these are reasons to use the `os.log` framework. 

## Gotchas

- Its API is _escaping_
- It's defaulted to `private`
- Triggering a sysdiagnose isn't easy
- There's a crazy amount of logs that happens every second on your phone. Most of them are logs that happen by the sytem. Hence you have to use the right filtering tools. 
- Until iOS15, Apple didn't provide you a way to query the app logs through code with the use of predicates. It only allowed querying the logs if the user performed certain actions to trigger a sysdiagnose. But that would create a file with a size of about 100-400Mb where all app and systems logs are there combined. I believe it's usually for about 2-3 weeks of data.

## Example:

```swift
import os.log 

import UIKit
import os.log

@main
class AppDelegate: UIResponder, UIApplicationDelegate {
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        Logger.appLifeCycle.log("didFinishLaunchingWithOptions")
        return true
    }
}

extension Logger {
    static let appLifeCycle = Logger(subsystem: Bundle.main.bundleIdentifier!, category: "APP_LIFE_CYCLE")
    static let viewLifeCycle = Logger(subsystem: Bundle.main.bundleIdentifier!, category: "VIEW_LIFE_CYCLE")
}

```

## Where do the logs show up? 

### During debugging - Shows up in Xcode console
[screenshot]

### After downloading from app store

- Shows up in macOS console in real time

- Can be fetched through a sysdiagnose

### Starting from iOS15 you can now query logs for the _current_ run 

```swift
/// - Attention this isn't `import os.log`. It's because `OSLogStore` is part or the `OSLog` framework. 
import OSLog 

    func getLogEntries() throws -> [OSLogEntryLog] {
        let subsystem = Bundle.main.bundleIdentifier!

        let logStore = try OSLogStore(scope: .currentProcessIdentifier)
        let oneHourAgo = logStore.position(date: Date().addingTimeInterval(-3600))
        let allEntries = try logStore.getEntries(at: oneHourAgo)
        
        // Filter the log to be relevant for our specific subsystem 
        // and remove other elements (signposts, etc).
        return allEntries
            .compactMap { $0 as? OSLogEntryLog }
            .filter { $0.subsystem == subsystem }
    }
```
<sub>This snippet was copied from [Daniel Kaplan's](https://twitter.com/sleepeasysw) post from [here](https://stackoverflow.com/a/70455883/5175709)</sub>

### Caveat
You can't retrieve logs from previous runs. I tried doing `OSLogStore(scope: OSLogStore.Scope(rawValue: 0)!)`. It failed with the following error: 

> The operation couldn’t be completed. (Foundation._GenericObjCError error 0.) 

For more on that see [this](https://developer.apple.com/forums/thread/691093) thread in the forums.

### Our (Developer Technical Support) DTS with Apple

It was my first time doing it. It's a pretty straightforward process. Here's the response email we got from Apple:  

> Thank you for joining us for Tech Talks. Your office hour appointment
> has been scheduled and we look forward to talking with you soon.
> Please review the details below before your appointment.   Appointment
> Details Meet with Developer Technical Support Thursday, December 16,
> 2021 9:30 a.m. PST (see Calendar attachment for local time) 25 minutes
> https://appleinc.webex.com/appleinc/j.php?MTID=mrsdkji910a89a4dc9938dk690305
> 
> App URL: https://apps.apple.com/us/app/xfinity/id28902293 Topic:
> Notifications 
> 
> Question: We use a notification extension for "rich notifications" to
> provide a thumbnail along with the notification. Most of the time the
> image is displayed but sometimes the notification is displayed without
> the image. Analytics logged in the app extension validate that the
> image was downloaded and stored. Trying to figure out how/why the
> image will sometimes not display. 
> 
> Getting Ready Learn how to join a Webex meeting on desktop, mobile, or
> web. Learn how to share your screen to demo your app or show other
> items. Quit any applications that aren't critical to your appointment
> to reduce CPU and network bandwidth usage. If possible, connect to
> your network via Ethernet instead of WiFi and reduce any home network
> traffic. Test your internet connection speed to ensure it meets the
> minimum bandwidth requirements for Webex. If you plan to show an Xcode
> project, set the theme to Presentation Mode in the Xcode Fonts &
> Colors preferences. Learn how to use Webex Accessibility features. If
> you can no longer attend, please cancel your appointment. If you
> cancel your appointment, you won’t be able to request another
> appointment for this topic this week. Joining the Meeting Use
> headphones with a microphone for optimal audio quality. If your
> network connection is limited, consider using the Webex Call Me
> feature and connect to audio using your phone (tolls may apply). Join
> the meeting by following your meeting link under Appointment Details.
> Recording and/or taking screenshots is not allowed; please prepare to
> take notes. Please don’t share confidential information. If you had
> issues during your appointment, contact us with your office hours ID
> KUH85G4AA0. Your appointment will conclude promptly at the designated
> time. 
> 
> 
> Apple Developer Relations

