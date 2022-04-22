---
title: "What is UITransitionView about?"
date: 2022-02-01T14:21:03-05:00
category: ['Xcode']
tags: ['Xcode', 'iOS', 'view debugger']
slug: "what-is-uitransitionview-about"
editPost:
    URL: 'https://github.com/prohoney/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

I was using Xcode's View Hierarchy and noticed this `UITransitionView` in my view hierarchy. 

```
...
    UIWindow
        UITransitionView
            FooVC
        UITransitionView
            BarVC
```

This was odd because I was expected a view hierarchy as such:
```
...
    UIWindow
        UITransitionView
            FooVC
                BarVC
```

What made it more perplexing was that the _canvas_ was showing things correct, but the _View Hierarchy_ didn't make sense.

To be clear on jargon, the following is the name of each section of Apple's view debugger:

![View Debug Hierarchy](images/db_view_debug_overview.png)


I googled Apple documents, but found nothing on `UITransitionView`. It's private API. 
I had suspicions for why I was seeing it... So I opened up a sample project to test things out. 

The following is the view hierarchy I got for **presenting** the yellow VC on another VC. 

![UITransitionView in View Debugger Hierarchy](images/uitransitionview-in-view-debugger.png "Seems that the presented and presenting stacks are on different `UITransitionView`s")

Expanded Hierarchy is as such: 

![UITransitionView in View Debugger Hierarchy](images/full-view-hierarchy-of-a-presentedVC.png)


## Conclusion

If you you're seeing **multiple** `UITransitionView` it's likely because you're **presenting** one viewcontroller over another. One `UITransitionView` is for the _presenting_ nav stack while the other is for the _presented_ nav stack.
