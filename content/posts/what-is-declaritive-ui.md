---
title: "What is Declarative UI"
date: 2022-06-14T13:41:34-04:00
draft: true
description: "Do you keep hearing Declarative UI without know what it's about?"
category: ['SwiftUI']
tags: ['swiftui', 'declarative-ui']
---

### Swift is a declarative language

With UIKit, you had to scan your code to see how it reacts to certain view life cycle events. 
With SwiftUI, you look at the top of your code. See all the sources of truth that your view has. Example

```
class ViewController: ViewDidLoad {

    viewDidLoad() {}

}
```
This can be very restricting i.e. if you ever want to mutate a property, then you can't do it without marking it as a `@State` or other kinds of observables. Example: 

Beyond that you shouldn't update a state variable from within the block that's triggering it. 
Example, within a normal body block, you shouldn't update a state variable. 
You're only suppose to update state variables from within actions, network calls i.e. things that require some form of trigger. 
https://www.hackingwithswift.com/quick-start/swiftui/how-to-fix-cannot-assign-to-property-self-is-immutable


But then on top of that, you shouldn't update a state variable. Not sure what exactly causes the purple warning though for state changes within state changes. 
https://www.hackingwithswift.com/quick-start/swiftui/how-to-fix-modifying-state-during-view-update-this-will-cause-undefined-behavior


Also you're less doing, leading, trailer, this to that view. It's more of padding, spacing. This are more verbose

To facilitate this, SwiftUI normally appreciates if you create views with initializers that take a lot default params. This way the call site is cleaner. 