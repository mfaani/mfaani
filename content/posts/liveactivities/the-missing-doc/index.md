---
title: "The Missing Doc"
date: 2025-03-14T11:22:05-04:00
draft: true
---

I recently went on a quest for Live Activities. There's a ton of gotchas or subtle notes that aren't clearly mentioned. Think of this post like an unofficial rfc. 

-----

# Intro
## Example app - wwdc code - docs confusion points 

I saw Apple's videos and docs on ActivityKit. They do a decent job of showing you 80% of the API and how Live Activity works. The remaining 20% is an enigma though. Additionally parts of their docs on how to begin a live activity from Push Notification was incorrect. I'll try to address those all .  

## What's the difference between Live Activity and Dynamic Island?

- Dynamic Island is a physical location of certain newer models. First introduced in iPhone 14 Pro, and then on every model since then. 
- Live Activity is an Apple feature. Think of it as a shape shifting notification.
- The Live Activity appears in the dynamic island when the phone is unlocked. If a phone doesn't have a live activity then it appears as a notification 

## What forms does the Live Activity take?
The Live Activity can appear in multiple forms: 
- Dynamic Island
    - Compact 
    - Expanded
    - Minimal
        - Attached
        - Detached
- Lock Screen

## Does the Dynamic Island appear when app is in foreground?
No. You need to have your own in-app UI for this. 

## How long can a Live Activity remain on the screen? 
- A Live Activity can be active for up to eight hours unless its app or a person ends it before this limit. 
- After the eight-hour limit, the system automatically ends the Live Activity, and immediately removes it **from the Dynamic Island**
- However, the Live Activity remains on the **Lock Screen** until a person removes it or for up to four _additional_ hours before the system removes it — whichever comes first. As a result, a Live Activity remains on the Lock Screen for a maximum of 12 hours.

## How can I cancel / end a Live Activity? 
You have think of it more as a dismiss. When you dismiss a Live Activity timer then you're NOT stopping the timer. You're just removing it from your dynamic island. To give you another example, if you dismiss the live activity of an Uber ride, then it does NOT cancel the ride. It just removes it from your dynamic island / view. If you wanted to cancel a live activity, then you can either add cancel buttons onto the live activity, and then allow users to cancel the the timer using certain actions associated with the notification or allow users to tap on the live activity, open the app and then cancel / end it. 


- From the compact mode, you can swipe left on its **center**
- From the expanded mode, you can't swipe left. 
- From the lock screen mode, you can swipe left and then click on the 'clear' action. 

NOTE: 



## Docs are incorrect for how to start a Live Activity from Push 

## Ticking between activities 

# What is a Live Activity
## How can I dismiss a Live Activity?

## The four sections in expanded view

## Doesn’t require authorization. Though I suspect it works like a provisional. Not sure.

# Architectural limitations

## Budget & Plist

## Backgrounding

# Development - General notes

## How can I share classes between widget and app? ## Can't use all libraries that your app library use. Because of `shared` and other reasons. 

## Previews are a in pain!!! Constantly check the project build logs and the preview error. Also the app seems to be built even though it shouldn't be deemed a dependency. ## Removing the swiftlint saved me 10-15 seconds on average...

# Development - Token

## Token variances and how to acquire them. Frequency of change

## Access to Static variables vs instance variables

## Updates (to token, to content, activity life cycle)

# Debugging
## `timestamp`

## lots of stuff in forums. But hard to find. 

## Getting more information from the `Console`. 

## Can you send Live Activity notifications to simulator? 

## Can you drag and drop payloads into the simulator?

## How can extract the key from the p8 file?

## Updating an incorrect json fails silently without you noticing it. 

## Encoding



# Improvements

## Dismissal vs Actions

## Alert

## Tips for reusing views


# Open Questions

## App Intent ???

## Animations & AudiKit + PlayPhone (whatever it's named) have special animations. So does Countdown labels. 

- Numerical 
- `contentTransition`. It's actually a bit of a paradigm shift for me. Because you typically need go animate something from `a` to `b`. 
Example: Animate the width from 100 to 200. However it seems to be different here. That is: 

your view is currently showing 100. If it ever needed to show 200 in future, then just apply this animation transition to it — regardless of 100 -> 200 or 200 -> 100

## Ending / `dimissal-date`

## In general how should apps do things for their app launch in background? should you have a shared block that is ran for all things that get launched for both: 
- foreground
- background

Example if I do something to force the app to listen to location changes, and then the app is later launched into the background, then whatever code that processed that background change must get launched and processed accordingly — without user navigating to where to the observance is set. Basically the reaction to such app launches need to be moved to app launch time. 

I wonder what the impact of this is on your app. Like should I just always be setting all kinds of observations when app is launched into the background? or only set those up if the app is launched due to an event associated with it? But then what if there's a follow up even that's not associated with the previous app launch? 

App is launched, I setup observance assocaited to that specific event, now comes another event, but since the app is launched I just assume the the observance has happened which is then an incorrect assumption!

## In-app experience ????

# Other

## Code after for loop await may never run!

## References 