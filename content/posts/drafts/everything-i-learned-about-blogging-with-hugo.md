---
title: "Everything I Learned About Blogging With Hugo"
date: 2022-04-23T04:17:58-04:00
category: 'content-creation'
draft: true
tags: ["hugo", "content-creation", "blogging"]
---

- Instead of just sharing a link or what originated, share a good teaser of your post and just add a link. That way you at least get traction from where you posted it (Twitter, Slack). If anyone was interested they'll click to see more.
- Don't over explain things. Or at least don't over explain too many things in a blog post. Maybe just go deep for a single thing. Not 5 things at once. Or if someone has already done it, then you don't need to re-say it. Just link to theirs. 
tldr it's hard to have breadth and depth in the same post...
- Google Analytics
- SEO
- git submodule
- Highlighting 
```swift { hl_lines=["16-20"]} 
class Foo {
    var name = "Jack"
}

```
- Add two spaces to create new line after a line end. Otherwise hugo will just continue the line. This is something hard to grasp. Because of this I usually manually review my rendered post with `hugo server -D`
- The timestamp of your posts is important. Like you may have actually started your draft 10 days ago, or might have even started the draft before a currently published post. Because of that you should adjust your current timestamp to the publish date...
- With images, overtime your static folder will turn into a big graveyard. So it's best that you re-structure your posts into 
    - Bad way: 
        - Posts
            - post1
            - post2
        - static
            - imageA
            - imageB
    - Good way: 
        - Posts
            - post1
                - index.md
                - images
                    - imageA
            - post2
                - index.md
                - images
                    - imageB
- Blogging vs SO
- enter new line 
- config file 
- css basics
- css variables
- marketing your posts. Slack, Twitter, Stackoverflow. Promoting others...
- fun learning curve
- when to post? 
- Spell check
- Netlify previews
- Problems so far
- Known issues...
- Don't just post images. Post videos/gifs as well. They make your website **rich**. Example instead of screenshot of Apple's Playground, actually take a short video. 
- Shortcodes
- Description
- If the post is getting big, then split it. The key to being able to split it, is to to have good headers. 
- Once you have good headers, then you have 4-5 drafts in parallel. They might be related. But **don't** worry about associating posts together yet. Just try to get each post contained. Like don't talk about 5-6 different compiler flags in one single post. Split them up. 
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