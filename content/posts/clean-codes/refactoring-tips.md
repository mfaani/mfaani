---
title: "Refactoring Tips"
date: 2022-09-02T19:36:13-04:00
draft: true
---

It's the first time I'm doing a refactor at this scale. I'm changing the logic handling code that touches the heart of our feature. This post is mainly to mention the pain points and considerations. IMHO just being aware of the pain points can give you the confidence that you're doing it wrong. Refactoring is challenging!

- Master how the current code works. 
- Write down **why** the current code is bad. Put it down in several bullet points, or create diagrams of the current bad code. 
- Write down **how** you want to improve the code or create diagrams of your proposed code. 
- I often use pen and paper to get the current diagram down or write _special_ todos for myself. Example I write a comment and do: 

```
// @self:do: replace `Foo` with `Bar`
// @self:mistake: we should use `fooFlag` instead of `barFlag`
```

Once I push up my code for code-review, I remove the todos I wrote. 

- The first step to refactor is to create a ticket. Then Spike. Then create more tickets. Try to break it down. 
- If needed write down the new signature. 
- Do refactor incrementally. 
- The bigger the codebase is, the more time it takes for you to come up with a good architecture. Often the code-change is very little, but just coming up with the correct mindset and steps take time. Might need to re-do your refactor. It's totally fine to refactor your refactored code again. Make sure you communicate to your team members that a lot of time is needed. 
- The bigger the refactoring is, the more QA work that will be needed.
- Create a diagram in incremental steps
    - High Level diagram. Remove edge cases. Example: `Enter Flow >> Check Device Model >> Check if user is allowed to Activate Device >> Activate Device >> Exit Flow`
    - More Detailed diagram. `Enter flow from ScreenA >> Check Device Model >> Check if user is allowed to Activate >> Check if device is online >> setup device >> If success then Exit flow | If failure then retry`
    - NOTE: ANY TINY DISCREPANCY between your diagram and the actual code will turn into a source of bugs. Example: Our diagrams weren't considerate of feature flags. And so in my new architecture I wasn't considerate of exit early due to feature being unavailable. The high levels are meant to simplify the steps. THEY ARE NOT A SOURCE OF TRUTH. 
    - It's often better to have multiple small yet accurate diagrams.
    - For anything that's not covered in the diagram leave disclaimers. You as the author might know what you left others. Others won't know it. 
    - Version your diagram. 
    - Version control your diagram as well.
    - Diagram should have stages. Example: 
        - Eligibility Check 
        - Feature Flag Check
        - Error Stage
        - Success Stage
        - Early User Exit State
        - Anchor Stage
        And perhaps you could have your high level / less detailed diagram made up just
    - No diagram is perfect. You can't mold a real flow into a perfect square. At the very end there will be some rough edges. 
        - Diagrams are just to help you see things easier. Reality is the ultimate source of truth. Diagrams are the closest possible manifestation of it. 
        - There are diminishing results with trying to build a perfect diagram. 
        - What you must do is to never let your diagrams go stale.
    
  You can have a more detailed diagram than this too. The note is to do it steps. Often using writing down on a paper initially makes things a whole lot easier for me.     

### Other tips:
- Use `git worktree` to be able to compare your changes with code of your `main` branch in realtime. I compared two branches of my project. It's easier vs. doing `git diff` to go through your changes.
    - Note: `git worktree` are good for comparing lines of code. However things get messy when you **run** the two projects. This I believe is what the git worktree man page refers to [here]()
    - Be careful to make the changes / run / test the correct worktree. Don't ask how I know this. 🙃
- If you're refactoring code you haven't wrote yourself, then it might be a good idea to have some meetings with the person who actually wrote the code — if possible. 
- Often moving multiple properties into a new type helps
- Move functions into an extension
- Rename variables
- You might be doing the refactor if a feature has grown over the years or someone owned a single person owned a good portion of the logic and it made sense to them only. Now this know-all person is both your enemy and best friend. 
    - best friend: Because they have the most knowledge on the matter
    - enemy: Because to them things actually do make sense. They could be blind to the complexities of the current architecture because they're used to it. And please don't take this the wrong way, if I owned all the logic of something, then things would be easier to me vs. others who are seeing it for the first time. Often I can't reason with cold I wrote 2 months ago. 
    The point I'm trying to make is, even because you think someone's code needs a major refactor, don't think that person's insight has no value. For me it had the most.
- Add documentation. Documentation have a huge rubber-🐤 effect.
- Add new variables
- You shouldn't be doing similar things in different (parallel) ways/
    - You shouldn't exit by calling `A.exit` and then also doing `A.b.exit`, unless it refers back to `A.exit`. or calls the identical function that `A.exit` calls. I know you're looking at the code and are like, that's easy to grasp. 
    - It is easy to grasp. But it's not easy to reason with. Tracking a single code path is easier vs. tracking two. You don't know why/where its hit. Or why you have two. 
    - It's better to have a single point of entry and single code-point of exit. 
    - To say it differently: you shouldn't have parallel code paths that do similar things. You can start from two different points. But you need to make them join **immediately**. 
- Computed properties are great
    - Try finding identify what was it truly that the decision was based off of. Example: You reload the home screen for a certain enum (Ex successful activation). However the real reason you reload the Home screen isn't because it's a successful response. It's because an activation resulted in **change**
- As you do QA/unit-test you may learn new things about your architecture. If that happens then you might need to update your diagrams again. 
- Often you'd want to 
- Remove variables
- Remove dead code. Just make a commit for that. And be done with it. 
- I really came to appreciate `typealias`. We didn't use it and now I have to change every place in where we had `(Event) -> Void` to `(CustomEvent) ->Void`. Had we used typealias, it would have been a single place. 
- Often similar properties exist that you can merge them together. 
- Your goals should be: 
    1. Get the architecture diagram correct
    2. Start writing the code, but don't try to write implementation for every piece of code. Just try to get it compile. I got my code to compile by replacing lots of assertions in place of returns.  Or often if I didn't know what to do then I just left a `fatalError("not sure"), so my code compiles and I don't get stuck on it, but also it's clear that I need fix something later on. 
    3. Replace the assertions with real returns
- When the refactor is big. Then it means the Pull Request will be big. There will be a lot of conversation. Having a lot of conversation makes the page loads slow and because of the numerous comments, things become confusing. This is on top of the architectural load. For this reason it's best to not make incremental PRs. But if you can't do that then it's perhaps a good idea to just do a screen share with your PR reviewer and LIVE CODe. Try not go back and forth. It can be very toiling especially towards the end. At recall at some point the conversation got so long and the changes I made, I had trouble understand what my comment meant. It was as if I was having memory lose...

tldr don't think of it as pure refactor, it's better to think of clean code.
Also if you can't make sense of similar properties exist, or dead code, or something is upside down, then it may actually be for no good reason, but it also might have a reason.

One other reason you're in this place and you shouldn't is likely because: 
- You didn't allow unrelated changes get into PRs. Example a developer realized some variable name was bad. Example `existingName: Bool` is a boolean. But its name doesn't convey that. It should be refactored into `hasExistingName: Bool`. Yet PR reviewers say "to keep the PR focused, we shouldn't make this change now here". Which tbh does make sense. It's a good principle to follow. Yet the trade off is worth it. Perhaps the developer can make such changes after getting approvals for the main logical change of the PR. 

## IDE tips 
- When you've made a big refactor, then tests will fail. 
- Try to commit the code changes you've made. Then update the tests. This allows you to isolate the tests changes you've made. 
- Often you need to fix the tests. To do that, it's first best to just get it to compile. 
    - Then find out how your IDE allows you to just jump between changes in code. 
    - Then try to fix the change by making the test pass. 