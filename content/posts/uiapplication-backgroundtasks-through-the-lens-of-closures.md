---
title: "UIApplication BackgroundTasks Through The Lens of Closures"
date: 2022-02-22T09:30:28-05:00
category: ['iOS', 'Swift']
tags: ['iOS', 'background-task', 'closures', 'UIApplication']
editPost:
    URL: 'https://github.com/prohoney/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

## The sync closure

```swift
func processTwoNumbers(_ num1: Int, _ num2: Int, handler: (Int, Int) -> ()) {
    handler(num1, num2)
}

processTwoNumbers(10, 22, handler: { num1, num2 in 
    print(num1 + num2)
})
```

In the above, the `handler` is called in a sync matter. Same as below

```swift
(1...100).forEach { val in
    print(val)
}
print(101)
```

**Uniqueness:** The block passed in is executed *immediately* at the call site. 

## The async closure — block isn't stored.

```swift
enum Result {
    case success(Data?)
    case failure(Error?)
}

func startFlow(handler: @escaping (Result) -> ()) {
    let url = URL(string: "https://i.imgur.com/7oZTmIC.jpeg")!
    
    let task = URLSession.shared.dataTask(with: url) { data, response, error in
        if error == nil {
            handler(.success(data))
        } else {
            handler(.failure(error))
        }
    }
    task.resume()
}
```

**Uniqueness:** `handler` block is called by the `startFlow` function. Execution of the block is within the function itself. It gets called after some asynchronous operation. 

## The async closure — block is stored. 

So you'd have the following definitions: 

```swift
enum Result {
    case success
    case error
}

class FlowManager {
    var _exitHandler: ((Result) -> ())?
    func startFlow(exitHandler: @escaping o(Result) -> ()) {
        _exitHandler = exitHandler
    }

    func handleFlowExit(result: Result) {
        _exitHandler?(result)
    }
}
```

**💡Uniqueness:**  `exitHandler` is **not** called by the `startFlow` function! `exitHandler` is called later. You don't know really when. You'd have to look where it gets stored. And then where that stored block gets executed. 

The usage would be something like: 

```swift
class Controller {
    var flowManager = FlowManager()
    func start() {
         { [weak self] result in
            switch result {   
            case .success: self?.showSuccessScreen() // Line A
            case .error: self?.showFailureScreen // Line B
            }
        }
    }
    
    func showSuccessScreen() {
        
    }
    
    func showFailureScreen() {
        
    }
}
```

#### Ask yourself: When do Lines A and B get executed?
Obviously, unlike the first example, they do not get executed before `start()` is finished. 

More importantly, unlike the second example, the execution of the block passed in, is **not** controlled by the `startFlow` function. Instead the block is executed whenever `handleFlowExit` is called. But that's not clear from the callsite at all!

#### Why would you want to do something like this?
When you want to tie blocks of code together — even though they're not chained after another i.e. they're just semantically related, but one block will execute now, the other depending on UX / business logic will execute later.

A real world example would be:  
You start a flow. Your flow has five view controllers. At then end of the fifth view controller, you pop the viewcontroller and come all the way back to your root viewcontroller. Your `handleFlowExit` is called then, while its block is defined within the `startFlow` function. 

#### Could the code be written differently?
Sure. It's just that the _event_ is multiple classes away from the class that can actually _handle_ the event and you didn't architecture things to be in a delegate based way. 

#### So why did I mention all this? 

To say that the following signature: 

```swift
func startFlow(handler: @escaping (Result) -> ()) {
```

can operate differently between the second and third example. Having good documentation becomes key for diffrentiating and clarifying when the callback is called by the function itself vs another function/event. 

## So how is this related to background tasks?
Well it just so happens that background tasks are of the third way. 

The `expirationHandler` is **NOT** executed by `beginBackgroundTask(expirationHandler:)` function. 

Rather the block gets stored. And executed later — if the timeout was near and you haven't called `endBackgroundTask(_:)` yet. I took my many many looks at: 

```swift
var backgroundTaskID: UIBackgroundTaskIdentifier = .invalid
backgroundTaskID = UIApplication.shared.beginBackgroundTask() {
    UIApplication.shared.endBackgroundTask(backgroundTaskID)
}
```

And always thought `UIApplication.shared.beginBackgroundTask()` is getting executed similar to our second example i.e. its block is about to get executed and the delay is just do due general async mechanics. That's incorrect. It's better read as such:

```swift
UIApplication.shared.tellOSIhaveATask(expirationHandler_whichGetsStored_andToBeCalledIfNeeded_inAbout30Seconds: {
    immediateatlyStopTask()
    UIApplication.shared.endBackgroundTask(backgroundTaskID)
})
```

**tldr Think of `expirationHandler` as a parameter that gets passed around. It's semantically different from most other compeletionHandlers iOS has**

### UIApplication internal implementation

From my understanding the internal architecture of `UIApplication` is something like this: 

```swift

import Foundation 

typealias UIBackgroundTaskIdentifier = String

class UIApplication { 
    var _expirationHandler: (() -> Void)?
    var currentBackgroundTaskID: String? 
    func beginBackgroundTask(expirationHandler handler: (() -> Void)? = nil) -> UIBackgroundTaskIdentifier {
        _expirationHandler = handler
        scheduleExpirationCheck()
        return UUID().uuidString
    }
    
    // After 25 seconds, checks to see if task is expired. Otherwise it will call the expirationHandler. 
    // The `expirationHandler` should call `endBackgroundTask`. Nonetheless due to programming mistake it's possible that it doesn't get called. 
    func scheduleExpirationCheck() {
        Timer.scheduledTimer(withTimeInterval: 25, repeats: false) { [weak self] _ in
            guard let self = self else { return }
            if self.currentBackgroundTaskID == "invalid" {
                // task has already ended. We don't need to end it.
            } else if let currentBackgroundTaskID = self.currentBackgroundTaskID {
                // task took too long. Let's call its expirationHandler before we suspend the app
                self._expirationHandler?()
                self.terminateAppIfNeeded()
                
            }
        }
    }
    // Marks a taskIdentifier as invalid. 
    func endBackgroundTask(_ identifier: UIBackgroundTaskIdentifier) {
        currentBackgroundTaskID = "invalid"
    }
    
    // Is called 5 seconds after the expirationHandler. It terminates the app if user didn't end the task in their expirationHandler.
    func terminateAppIfNeeded() {
        Timer.scheduledTimer(withTimeInterval: 5, repeats: false) { [weak self] _ in
            guard let self = self else { return }
            if self.currentBackgroundTaskID == "invalid" {
                // good! programmer called `endBackgroundTask(self.currentBackgroundTaskID)` from within their `expirationHandler`
            } else {
                // Terrible code!
                fatalError("terminate the app, as 25 + 5 seconds has passed but the user never told the OS that the task has ended!")
            }
        }
    }
}
```

### How are things different in +iOS13?

You don't use `beginBackgroundTask(expirationHandler:)`. Instead you use the `BackgroundTasks` framework. A lot of things change with that, but that's beyond the scope of this article. The API is cleaner i.e. specifically for expiration, there is a [property named `expirationHandler`](https://developer.apple.com/documentation/backgroundtasks/bgtask/3142234-expirationhandler) that you need to set:

```swift
task.expirationHandler = {
    endTask()
}
```

## Summary
Not every block is to be considered a `completionHandler`. Some blocks get stored for later execution. Some blocks may never get executed. It all just depends on the logic. The `expirationHandler` parameter of the  `beginBackgroundTask` is a block that gets stored and may or may not get executed later.


## Also see

[Apple forums: UIApplication Background Task Notes](https://developer.apple.com/forums/thread/85066). It addressses a lot of commons questions. 
