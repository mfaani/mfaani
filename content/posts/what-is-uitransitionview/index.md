---
title: "What Is UITransitionView"
date: 2022-02-01T14:21:03-05:00
category: ['Xcode']
tags: ['Xcode', 'iOS', 'view debugger']
---

I was using Xcode's View Hierarchy and noticed this `UITransitionView` in my hierachy. 

```
...
    UIWindow
        UITransitionView
            FooVC
        UITransitionView
            BarVC
```

What made it more perplexing was that the cavnas was showing things correct, but the _View Hierachy_ didn't make sense.

To be clear on jargon, the following is the name of each section of Apple's view debugger:

![View Debug Hierarchy](images/db_view_debug_overview.png)


I googled Apple documents, but found notihing on `UITransitionView`. It's private API. 
I had suspicions about what it was... So I opened up a sample project to test things out. 

The following is the view hierachy I got for **presenting** the yellow VC on another VC. 

![UITransitionView in View Debugger Hierarchy](images/uitransitionview-in-view-debugger.png "Seems that the presented and presenting stacks are on different `UITransitionView`s")

Expanded Hierarchy is as such: 

![UITransitionView in View Debugger Hierarchy](images/full-view-hierarchy-of-a-presentedVC.png)


## Conclusion

If you you're seeing `UITransitionView` it's likely because you're **presenting** one viewcontroller over another.
