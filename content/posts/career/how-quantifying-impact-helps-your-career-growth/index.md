---
title: "How Quantifying Impact Helps Your Career Growth"
date: 2024-11-15T12:22:07-05:00
category: [career]
tags: [career-growth, metrics, impact]
description: "Often as engineers we do ourselves a disservice and just do the hard work without measuring its impact. This post gives you strategies to promote yourself"
---

Learning from my product peers, TIL if you’re making an impactful change, it’s not just about the change itself—it’s about how you communicate its success. A compelling story can amplify your impact, but every great story needs a foundation of data to back it up. Logs, metrics, and dashboards are essential tools that help you visualize, quantify the problem and measure the impact of your solution from the perspectives of:

- Users
- Engineers
- Product Managers
- Company

With these numbers in hand, you can craft a narrative that not only highlights your achievements but also resonates with stakeholders across all levels.

Example if your change causes less errors (or more users down a funnel), then:
1. Previously 2000 users a day were seeing errors, now only 600 see the error. Less chatting with the AI chat box. 
2. Are going from 5 triage calls (5 engineers each involved for 2hrs for each triage) a month down to 3 triage calls a month?
3. The success rate of our flow has increased by +1400 a day which means 5% more success rate. 
4. The financial impact of 1400 more users is is worth 1400 * $5 per day, also we're now having 1400 less chat box interactions which is reduces load on our servers.

If you didn’t figure out a way or haven’t spent time in capturing the metrics then don’t release it. Otherwise your change isn’t measured / celebrated / understood / shared / marketed / rewarded.

Deferring the dev work to add logs to a future release **will work against you** if you can’t store all the data or don’t have metrics *before* the change. This requires you to have proper process and understanding set up with your Manager and Product Manager so that you don't rush to release.

It’s possible that your change can have an unexpected negative impact. You won’t know of it unless you have metrics / alerts / baselines in place.

## Note
The 2nd step often requires you to find/reach out the right (product) person and have them help you figure the $ value for it.  
The more the change impacts the users, the more you have to get Product involved to help you come up with that business / product impact.

Also often you do something that has a huge impact on engineers, but the combination your director, vp, culture, business goals don't appreciate what you did — even if it saved the company 2000 engineer / hrs a year. This is why it's often better to identify something that aligns with the existing goals of the company / director / vp. This way you don't have to convince them. You're just fulfilling a _pre-defined_ goal. 

> Basically you have be good at the office politics game and choose things that matter to those that promote employees. 

## Examples

Apple Product Launches do a great job at highlighting the user/product impact or the delta of what their doing vs others/before

![](M3-perf.jpg "M3 CPU Performance")

![](adoption.jpg "macOS vs Windows Adoption Rate")




## Summary 

Upon sharing my realization with my team members as a common shortcoming of us engineers, one of our company's most distinguished engineers - [John Riviello](https://www.johnriviello.com) immediately replied with: 

> Excellent Advice. Every time I’ve been promoted at our company, it’s because I had data like this to quantify my business impact, and I made sure to collect it every time I shipped something so I had an inventory of impact to reference. 
>
> One other note along these lines to be aware of, be careful about trying to tie time saved with money saved [How to Pitch a Project](https://blog.jonm.dev/posts/how-to-pitch-a-project/)

As someone who's recently became a tech lead, it took me a while to understand, regardless of my engineering abilities, I lack significantly in the domain of office skills such as metrics gathering and showing impact. Since realizing that, I've been accounting time for how to log the diff, not to rush releases, and most of all, the time that I have to spend (or delegate to a PM or Manager) to find a reasonable dollar value to be calculated.

