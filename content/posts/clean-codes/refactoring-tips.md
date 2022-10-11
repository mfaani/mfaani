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

- If needed write down the new signature. 
- Do refactor incrementally. 
- The bigger the codebase is, the more time it takes for you to come up with a good architecture. Often the code-change is very little, but just coming up with the correct mindset and steps take time. Might need to re-do your refactor. It's totally fine to refactor your refactored code again. Make sure you communicate to your team members that a lot of time is needed. 
- The bigger the refactoring is, the more QA work that will be needed.
- Create a diagram in incremental steps
    - High Level diagram. Remove edge cases. Example: `Enter Flow >> Check Device Model >> Check if user is allowed to Activate Device >> Activate Device >> Exit Flow`
    - More Detailed diagram. `Enter flow from ScreenA >> Check Device Model >> Check if user is allowed to Activate >> Check if device is online >> setup device >> If success then Exit flow | If failure then retry`
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
- You shouldn't be doing similar things in different ways
    - You shouldn't exit by calling `A.exit` and then also doing `A.b.exit`. I know you're looking at the code and are like, that's easy to grasp. 
    - It is easy to grasp. But it's not easy to reason with. 
    - It's better to have a single point of entry and single code-point of exit. 
- Computed properties are great
    - Try finding identify what was it truly that the decision was based off of. Example: You reload the home screen for a certain enum (Ex successful activation). However the real reason you reload the Home screen isn't because it's a successful response. It's because an activation resulted in **change**
- As you do QA/unit-test you may learn new things about your architecture. If that happens then you might need to update your diagrams again. 
- Often you'd want to 
- Remove variables
- Remove dead code
- I really came to appreciate `typealias`. We didn't use it and now I have to change every place in where we had `(Event) -> Void` to `(CustomEvent) ->Void`. Had we used typealias, it would have been a single place. 
- Often similar properties exist that you can merge them together. 
- Your goals should be: 
    1. Get the architecture diagram correct
    2. Start writing the code, but don't try to write implementation for every piece of code. Just try to get it compile. I got my code to compile by replacing lots of assertions in place of returns.  Or often if I didn't know what to do then I just left a `fatalError("not sure"), so my code compiles and I don't get stuck on it, but also it's clear that I need fix something later on. 
    3. Replace the assertions with real returns

tldr don't think of it as pure refactor, it's better to think of clean code.
Also if you can't make sense of similar properties exist, or dead code, or something is upside down, then it may actually be for no good reason, but it also might have a reason.