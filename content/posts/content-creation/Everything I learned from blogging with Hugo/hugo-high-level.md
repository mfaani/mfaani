---
title: "Hugo High Level"
date: 2024-04-30T04:17:58-04:00
category: 'content-creation'
tags: ["hugo", "content-creation", "blogging", "static site generator"]
description: "How does Hugo work? How are things structured? What are the main building blocks of Hugo?"
---

This is the 2nd post of ["Everything I learned from blogging with Hugo" series](https://mfaani.com/posts/content-creation/everything-i-learned-from-blogging-with-hugo/)

# Why Hugo?

- Hugo is a [Static Site Generator](https://www.cloudflare.com/learning/performance/static-site-generator/). Key is the word "static". It means: 

> A static website is made up of one or more HTML webpages that **load the same way every time**. Static websites contrast with dynamic websites, which load differently based on any number of changing data inputs, such as the user's location, the time of day, or user actions. While static webpages are simple HTML files that can load quickly, dynamic webpages require the execution of JavaScript code within the browser in order to render


- [Hugo Quick Start](https://gohugo.io/getting-started/quick-start/). Unlike other tools ([gatsby](https://www.gatsbyjs.com)or [Jekyll](https://jekyllrb.com)), there is no `npm`, `Gemfile` or `ruby` hell that you have to deal with. Though this also means that you may not be able to benefit from what `npm` offers unless it's offered for you in Hugo.
- It's super fast.
- Hugo has a semi easy building block system. Each theme has certain abilities that can be enabled as long as you add its appropriate metadata values. These metadata values are known as [frontmatter](https://gohugo.io/content-management/front-matter/).

# High Level

- I highly suggest you go through [this](https://retrolog.io/blog/creating-a-hugo-theme-from-scratch/) post. It's about how to create a hugo theme from scratch.
- Each theme has certain pre-configured layouts. A layout is a structure for a webpage. You can have layouts that inherit from a sort of base class. The base/root structure that all others inherit from is named: `baseof.html`. Every screen inherits from its layout structure. Every Hugo theme must have a `baseof.html` file. Then it also most have two main layouts as well. 
    - `single.html`: A layout for a single post.
    - `list.html`: A layout for a page that lists multiple posts.
- To be clear, you don't have to specify which layout you need to use. Hugo will just know based on the location and naming conventions of your markdown file. 
   - An `_index.md` file will automatically use the `list.html` layout.
   - An `index.md` file will automatically use the `single.html` layout.
  
### Quiz
Now let's just try _not_ to think in the context of Hugo, but just in the context of a website. What do you think each of the following links should represent?

 - mfaani.com/posts/first-post (a post)
 - mfaani.com/posts/second-post (a post)
 - mfaani.com (homepage)
 - mfaani.com/posts/devtools (a directory)
 - mfaani.com/about (a page)

First-post, second-post and about page use the `single.html` layout. They're just different posts.  
Both homepage and /devtools directory use the `list.html` layout. They query different _ranges_ of posts. 
- Homepage queries all posts. 
- `/devtools` directory queries only posts under that directory.

ATTENTION: For the homepage, you can add an  `index.html` in the layouts directory. It takes precedence instead of using `list.html` layout. 

 So for mosts sites you'd have `single.html`, `list.html` and a `index.html`. My blog doesn't have an `index.html` layout.

## Configuration
- [`Config.yml` or `Config.toml`](https://github.com/mfaani/mfaani/blob/main/config.yml) is where you have the website level configuration. Things like: 
  - Which theme to use. This means changing to a different theme can often be as easy as changing just the theme's name from your configuration file.
  - Website's main navigation menu. In my blog, the navigation menu contains: Blog, Search, Tags, About
  - Website's title
  - Google Analytics id. I used [Gideon Wolfe's post](https://gideonwolfe.com/posts/sysadmin/hugo/hugogoogleanalytics) to figure out my setup.****
  - Website's main language (If you have multiple languages)
  - Configuration of global settings. Example:
     ```
     ShowReadingTime: true
     ShowShareButtons: true
     ShowBreadCrumbs: true // to show parent pages of a subpage
     ShowCodeCopyButtons: true
     # Certain values of the `config.yml` file can be overridden in the frontmatter of an individual post. 
     ```

### Summary
- Every theme has something like a base class named `baseof.html`.  
- Single pages have a layout defined in `single.html`
- List pages have a a layout defined in `list.html`
- All of the above are within the theme. 

How Hugo decides to use which layout is merely based on whether it needs to render a single post or a page that lists multiple posts together. 

## Special files

### index.md
It's better to explain with an example. 

  ```
  content 
     posts 
       hugo findings
          imageA.jpg
          imageB.png
          index.md
       third-post.md
  ```
  If I all I had was the above, then it means I have two blog posts named: 
  - myblog.com/posts/hugo-finidings
  - myblog.com/posts/third-post

- `index.md` just makes things cleaner in a folder. Allows me to group a post and its resources (images, pdfs) in a directory. For more on that see [Hugo - Page resources](https://gohugo.io/content-management/page-resources/)
- If you don't use `index.md` then you can't reference to images in its directory. Only way to reference images is if your file is named `index.md` or `_index.md`. Only these two special files have the ability to group/bundle up resources with markdown files. 

- NOTE: I could have created a "third post" directory and then added an `index.md` within it. And things would have worked the same. If you don't have any resources/images to use then it's really becomes a matter of preference.

### _index.md
Allows us to add metadata (frontmatter) to a _directory_. It's not used for single posts. Example if I had: 

```
  content 
     posts 
       hugo findings
          postA.md
          postB.md
          postC.md        
          _index.md
       postD.md
```
then because I added `_index.md` then the I can access `myblog/posts/hugo-findings/` and see a list of all posts that are under the name of that directory. Without adding `_index.md` opening `myblog/posts/hugo-findings/` would result in a 404 error. 

In Hugo's terminology, `/hugo-findings` is known as a section. 
You can add frontmatter to the `_index.md_` much like any other post. Sample of `_index.md`:

```
---
Title: '⚡️ SERIES -  Optimizing App Size'
date: 2022-04-23T04:17:58-04:00
---

In this series I'll take about my struggles on how to be a better an iOS teacher, make better slides and what resources Apple provides. The series contains 10 posts. 
```

Make sure you see [here](https://stackoverflow.com/questions/76884002/how-can-i-setup-my-hugo-url-so-i-can-list-all-desired-urls-under-a-certain-url-p/76885448#76885448). Your blog can have as many   `_index.md` as the number of directories within your `posts` directory. 

#### What's the difference between `_index.md` and `index.md`?

| Syntax      | groups together | uses | creates |
| ----------- | --------------- | ---- | ------- |
| `index.md`      | a post and the resources referenced from its frontmatter and accessible to its directory  | `single.html` | a post |
| `_index.md`   | all posts and the resources referenced from its frontmatter and accessible to its directory        | `list.html` | a list page |


#### Can I use both an `_index.md` and an `index.md` file in the same directory?

From my experience you can't. It because unclear to Hugo if it has to process the `index.md` and make a post for the blog and use the `single.html` layout or if needs to process the `_index.md` and make a section/directory and use the `list.html` layout. For more on their differences see [here](https://gohugo.io/content-management/page-bundles/#readout)
 
## Other notes
- If you don't include `---` at the top of your post, then none of frontmatter will get processed. And you'd have a messed up title + if it's a draft, it will get published. 

- [What's the difference of Hugo vs doing things on my own with HTML, CSS?](https://g.co/bard/share/03eabb77a4f5)

[What's the difference between partial template and layout?](https://g.co/bard/share/2b83038a649a) and [here](https://g.co/bard/share/6ccf01f1f0b0). tldr:

> Partials (footer, head, headder) are small, **context-aware** components that can be used economically to keep your templating DRY.

"Context-aware" is an important term to understand. The partial will have global parameters of your site passed down to it. It would also page specific parameters passed down to it. 


- Going through [this](https://retrolog.io/blog/creating-a-hugo-theme-from-scratch/) this tutorial helped me signifcantly. Seeing this tree structure and the internals of `baseof.html`, `single.html`, `list.html`, and what goes inside layout and paritals was also very helpful. Unlike real themes, the tutorial is barebone and simple to understand. Highly recommend.

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

# Summary

- Use the config for global configuration of your theme
- Use frontmatter to configure your posts
- Hugo has distinct layouts for single pages vs pages that are a list of pages
- Each layout is made up of multiple templates. Templates are things like header, footer, head, etc. 
- Use `index.md` to bundle/group resources with a page
- Use `_index.md` to bundle/group resources with pages of a directory
