---
title: "The Missing Doc"
date: 2025-03-14T11:22:05-04:00
draft: true
---

I recently went on a quest for Live Activities. There's a ton of gotchas or subtle notes that aren't clearly mentioned. 

-----

# Intro
## Example app - wwdc code - docs confusion points 

## What's the difference between Live Activity and Dynamic Island?

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

## Animations ???

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