---
title: "Lesser known things about blogging"
date: 2023-10-04T04:17:58-04:00
category: 'content-creation'
draft: true
tags: ["hugo", "content-creation", "Hugo Formatting", "Hugo Frontmatter"]
---


## Other notes
- Blogging vs SO

- css basics
- css variables
- marketing your posts. Slack, Twitter, Stackoverflow. Promoting others...
- fun learning curve
- when to post? 
- Spell check
- Netlify previews

- Google search console
- If you make a change in your css stuff, then add images. For someone who's not a web developer, it becomes pretty difficult to understand which parameter I've changed...
    - or even explain what you learned in terms of `css`. Like for example if you learned how `>` works in CSS...
- Try using smaller header sizes if you have too many headers. This practice is ok. I've seen books often have smaller chapter sizes. 

- If you don't include `---` at the top of your post, then none of frontmatter will get processed. And you'd have a messed up title + if it's a draft, it will get published. 

What's the difference of Hugo vs doing things on my own with HTML, CSS? See: https://g.co/bard/share/03eabb77a4f5

What's the difference between partial template and layout? See https://g.co/bard/share/2b83038a649a

https://g.co/bard/share/6ccf01f1f0b0

> Partials are small, context-aware components that can be used economically to keep your templating DRY.

_context-aware_ is the more technical term that implies that `.Title` has a different meaning for each post or at the global level vs post level...

I think also as long as you create certain layouts then Hugo knows how to stich them up all together. 

- Go through this tutorial as well. It will help a lot. Seeing this tree structure was very helpful: 

```
.
├── archetypes
│   └── default.md
├── config.toml
├── content
├── data
├── layouts
├── resources
│   └── _gen
│       ├── assets
│       └── images
├── static
└── themes
    └── exampleTheme
        ├── LICENSE
        ├── archetypes
        │   └── default.md
        ├── layouts
        │   ├── 404.html
        │   ├── _default
        │   │   ├── baseof.html
        │   │   ├── list.html
        │   │   └── single.html
        │   ├── index.html
        │   └── partials
        │       ├── footer.html
        │       ├── head.html
        │       └── header.html
        ├── static
        │   ├── css
        │   └── js
        └── theme.toml
```

from: https://retrolog.io/blog/creating-a-hugo-theme-from-scratch/
- Hugo automatically takes the first 70 words of your content as its summary and stores it into the .Summary variable
- Instead, you can manually define where the summary ends with a <!--more--> divider
- Alternatively, you can add a summary to the front matter if you don’t want your summary to be the beginning of your post

- Adding `_index.md` at any directory will list that directory for you with its URL. 
 - You can do this for any directory, either top directory or anything
 - You can add extra text or html at the top as well...

 Explain Hugo without using its own jargon. 

 There



 You can have a partial for Next Prev pages. See https://youtu.be/ZFL09qhKi5I?t=1962

 and your next could be the next page in all your posts or just next in the current section 
- SEO

## Design
Tweaking your own theme or using your theme to its extreme is a great way to improve the design of your blog. It can also be fun to learn. With that said, often asking a professional web-developer to tweak and improve your blog's design, css can drastically improve the UX of your blog. Don't shy away from it.

 ## Pain points

