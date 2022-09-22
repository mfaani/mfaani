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


### Other tips:
- Use `git worktree` to be able to compare your changes with code of your `main` branch in realtime. I compared two branches of my project. It's easier vs. doing `git diff` to go through your changes.
- If you're refactoring code you haven't wrote yourself, then it might be a good idea to have some meetings with the person who actually wrote the code — if possible. 
- Often moving multiple properties into a new type helps
- Move functions into an extension
- Rename variables
- Add documentation. Documentation have a huge rubber-🐤 effect.
- Add new variables
- Remove variables
- Remove dead code
- Often similar properties exist that you can merge them together. 
- Your goals should be: 
    1. Get the architecture diagram correct
    2. Start writing the code, but don't try to write implementation for every piece of code. Just try to get it compile. I got my code to compile by replacing lots of assertions in place of returns.  Or often if I didn't know what to do then I just left a `fatalError("not sure"), so my code compiles and I don't get stuck on it, but also it's clear that I need fix something later on. 
    3. Replace the assertions with real returns

tldr don't think of it as pure refactor, it's better to think of clean code.
Also if you can't make sense of similar properties exist, or dead code, or something is upside down, then it may actually be for no good reason, but it also might have a reason.