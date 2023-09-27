---
title: "Everything I Learned About Blogging With Hugo"
date: 2022-04-23T04:17:58-04:00
category: 'content-creation'
draft: true
tags: ["hugo", "content-creation", "blogging"]
---


# Why Hugo?

- [Hugo Quick Start](https://gohugo.io/getting-started/quick-start/). Unlike other tools, there is no `npm` or `ruby` hell that you have to deal with. 
- It's super fast.
- Hugo has a semi easy building block system. Each theme has certain abilities that can be enabled as long as you add its appropriate metadata values. These metadata values are known as [frontmatter](https://gohugo.io/content-management/front-matter/). Examples:
  - You can add `tags: [swift, json, network call]` and it will then add the tags to your post. 
  - Add `showToc: true` and will show a table of contents for your post.

# Hugo High Level

- I highly suggest you go through [this](https://retrolog.io/blog/creating-a-hugo-theme-from-scratch/) post. It's about how to create a hugo theme from scratch.
- Each theme has certain pre-configured layouts. In Hugo a layout is a structure for a webpage. You can have layouts that inherit from a sort of base class. In Hugo the base/root structure that all others inherit from is named: `baseof.html`. Every screen inherits from its layout structure. Every Hugo theme must have a `baseof.html` file. Then it also most have two main layouts as well. 
    - `single.html`: A layout for a single post.
    - `list.html`: A layout for a page that lists multiple posts.
  
  ### Quiz
Now let's just try _not_ to think in the context of Hugo, but just in the context of a website. What do you think each of the following links should represent?

 - mfaani.com/posts/first-post (a post)
 - mfaani.com/posts/second-post (a post)
 - mfaani.com (homepage)
 - mfaani.com/posts/devtools (a directory)
 - mfaani.com/about (a page)

Both first-post, second-post and about page use the `single.html` layout. They just query different posts. 
Both homepage and /devtools directory use the `list.html` layout. They just query different ranges of posts. 
- Homepage queries all posts. 
- /devtools directory queries only posts under that directory

ATTENTION: For the homepage, you can add an  `index.html` in the layouts directory. It takes precedence instead of using `list.html` layout. 

 So for mosts sites you'd have `single.html`, `list.html` and a `index.html`. My blog doesn't have an `index.html` layout.

- [`Config.yml` or `Config.toml`](https://github.com/mfaani/mfaani/blob/main/config.yml) is where you have the website level configuration. Things like: 
  - Which theme to use. This means changing to a different theme can often be as easy as changing just the theme name.
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
   ```
  - The values of the config.yml file can often be overridden in the fronmatter of an individual post. 

### Summary
- Every theme has something like a base class named `baseof.html`.  
- Single pages have a layout defined in `single.html`
- List pages have a a layout defined in `list.html`
- All of the above are within the theme. 

How Hugo decides to use which layout is merely based on whether it needs to render a single post or a page that lists multiple posts together. 

## Special files

- **index.md:** it's better to explain with an example. 

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

  `index.md` just makes things cleaner in a folder. Allows me to group a post and its images in a directory. 
- **_index.md** allows us to add frontmatter to a directory. It's not a post. But just information to Hugo so it knows how to render a list page. Example if I had: 

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
then because I added `_index.md` then the I can access myblog/posts/hugo-findings/ and see a list of all posts that are under the name of that directory. 

In Hugo's terminology, /hugo-findings is known as a section. 
You can add frontmatter to the post much like another post. For more on that see [here](https://stackoverflow.com/questions/76884002/how-can-i-setup-my-hugo-url-so-i-can-list-all-desired-urls-under-a-certain-url-p/76885448#76885448)

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
- **Most important**. How can you make sure you don't have knowledge curse? Read your blog posts again after a month or two and see if it still makes sense. 
    - Or try the 2, 2, 2, 6 rule. 2 hours. 2 days. 2 weeks. 6 months. 
    - I just came back to two of my old posts. I actually totally forgot I had them written. It's a peculiar feeling. I found: 
        - typos and grammatical mistakes. 
        - I found things that I skipped without explaining them. 
        - I found ways to explain something better. 
    Basically the context of a fresh mind is more closer to an unbiased reader — while the context of an author's mind is like one on steroids full of pilled up knowledge.
- Once you've published, then check all your links and images. 
- Write things while they're fresh in your memory. Publish as soon as you'r 80% complete.
- Do I need to make a pull request? 
    - If it's a new post? No. Just add a good commit message
    - Is it a blog structural change? A Theme change? A CSS change? Something totally different? Then yes. Add a PR

- add a link to your website for EVERY image you've created. Helps your branding and gets you more clicks when the image is shared outside your post 
- Be wary about accidentally publishing a post. 
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
 ## Pain points
