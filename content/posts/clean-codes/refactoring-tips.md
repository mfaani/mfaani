---
title: "Refactoring Tips"
date: 2022-09-02T19:36:13-04:00
draft: true
---

- Write down **why** the current code is bad. Put it down in several bullet points, or create diagrams of the current bad code. 
- Write down **how** you want to improve the code or create diagrams of your proposed code. 
- If needed write down the new signature. 
- Do refactor incrementally
- The bigger the refactoring is, the more QA work that will be needed. 

### Other tips:
- Often moving multiple properties into a new type helps
- Move functions into an extension
- Rename variables
- Add documentation
- Add new variables
- Remove variables
- Remove dead code
- Often similar properties exist that you can merge them together. 

tldr don't think of it as pure refactor, it's better to think of clean code.
Also if you can't make sense of similar properties exist, or dead code, or something is upside down, then it may actually be for no good reason, but it also might have a reason. 