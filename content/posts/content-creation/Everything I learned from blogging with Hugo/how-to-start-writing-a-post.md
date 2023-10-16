---
title: "Tips for post creation"
date: 2023-10-01T04:17:58-04:00
category: 'content-creation'
tags: ["hugo", "content-creation", "blogging"]
description: "Discussion about when you should start blogging, the importance of getting early feedback and how to get it, how to market your posts and more"
---

I started blogging in Dec 2021. It's been a wonderful journey. Has enabled me to gather my thoughts in a far more structured way. The series is based on my setup of Hugo - Netlify - GitHub. I'll shared my knowledge in terms of Hugo knowledge, how to write a blog post and more. If anyone is interested in the Netlify setup, see [Hugo Quick Start](https://gohugo.io/getting-started/quick-start/) then [Netlify - Hugo setup]([@self](https://docs.netlify.com/integrations/frameworks/hugo/).

## When should you start creating a blog? 

> The best time to create a blog was 5yrs ago. Second best time is today - Chinese Proverb

Each and every one of us engineers have dealt with unique career paths, apps, teams, architecture, challenges. We all have a lot of insight to share. I used to write a lot on Stack Overflow. I still do, but blogging lets me explore my own passions more deeply and be less reactive to a question on Stackoverflow. It's a different and more fluid medium.

## Content Creation

- **Create drafts asap:** i.e. when you have an idea or started learning something. I've forgotten key details days after let alone months down the road. Don't just write bullet points, jot down anything and everything. Often when I come back to my bullet points: "css variables", it's been 6 months since. I've forgotten everything I've learned. At other times simply because I have my attention more focused on some new challenge, I think things I learned in the past are less important. Your brain tricks you a bit. 

> Content in your brain is like fresh food. If you don't eat when it's warm and fresh then it will go bad or just take a lot of time to defrost, or some more fresh food will come. Most things after they are learned become trivial and you think less of them. Truth is, anything can be fresh for the right person. 

- **Use headers** from the beginning. Don't let your post turn into a graveyard.
- **Links can die**, like Apple can remove a WWDC video. Make sure you annotate the link as much as possible. Example, don't just share the link to the session. Share the link and the name, year and minute of the session. That way folks can perhaps google it and find it or maybe download the video and upload it elsewhwere if allowed. 
- **Don't over explain.** If you're doing that, then usually it means you haven't found the right break down, wording, story, image to explain things. Or at least don't over explain too many things in a single blog post, unless your post is meant to cover a-z or be a post on the terminology of something. Small highly focused posts are often better. Some good examples of that are https://www.hackingwithswift.com and http://css-tricks.com. Most of the time they're laser focused. They just go deep for a single subject. Not 5 at once. tldr it's hard to have breadth and depth in the same post. 
  - Instead of writing one gigantic post >> write five small posts on different concepts. Then write a sixth post that summarizes and pieces it all together.
  - Often when I split a single post into multiple, I can rationalize about the post a lot easier, because our brains can only cache a certain amount of context at once.
  - The key to being able to split it, is to to have good headers.
- **Size matters:**: The bigger a post gets, the more refactoring, re-reviewing your code is required. Something that helps me to see the big picture all at once is adding a table contents to my post. Often I might opt of out using the table of contents, but after using it to help me see the big picture.
- **Topic selection:**
  - Be confident to post on topics that others have posted on before. Why?
    - Your tone, way of explaining or putting things together is unique.
    - They might have missed some details.
    - Things might have changed. Example how we automate things on GitHub is a forever changing subject.
    - Writing a post is the best way to learn about things. 
  - But also try to go for novel topics as well. Like some of my most read topics are on 'Cocoapods manfiest.lock file' or on 'Why can't Xcode show the caller?'
- **Use rich media** Post videos/gifs as well. They make your website **rich**. Example, instead of a screenshot of Apple's Playground, actually take a short video and demo it. Videos have audio, show UX far easier, quicker. You don't need to use them in every post, but just that you shouldn't shy away from them. If you end up with rich media, then make sure it's just 5-60 seconds long. 
- **Use correct dates:** If you stared the draft two months ago, but published today, then make sure the date readers see assocaited with the post on your blog is the 'publish date'. Otherwise they might think the post was from two months ago and feel its dated. 

## Benefits of getting early feedback 

- Share a [Netlify preview](https://docs.netlify.com/site-deploys/deploy-previews/) of your post with others when possible. It's a really easy way to get help from a group of experts in a Tweet or in Slack. Often typos, but a lot of time people will come back to you and be like:
  - "Your message in your post isn't clear, you're talking about X, then suddenly jump to Y"
  - "This line is just wrong. You've misundertood what *Foo* means. That's what *Bar* is"
  - "Use this documentation, blog post, other slack thread" to improve/deepen on your post.
- Often if you publish and then ask, people would be less caring. Previews are inticing much like how an early preview of a movie is. It makes the readers feel special. They truly are.

## How can you make sure you don't have a knowledge curse and things are clearly explained?
Come back to the post in 2 days, again in 2 weeks, again in 2 months, again in 6months and review them. See if everything still makes sense. 
I just came back to two of my old posts. I actually totally forgot I had them written. It's a peculiar feeling. I found: 
  - Typos and grammatical mistakes. 
  - Things that I skipped without explaining them. 
  - Better ways to explain certain things. 
Basically, the context of a fresh mind (who might have forgotten some things) is closer to that of a reader with a blank slate — while the context of an author's mind is like one on steroids full of knowledge and context.
- Once you've published, then check all your links and images. Checking your links is important because it's not something you can figure out by reading the markdown. You have to render it.   

> The only way to connect your past self with your current self and future self is if you have things recorded or written. And each of these instances of you are different from the other.
## Marketing your posts

Instead of just sharing a link or what originated, share something valueable from your post along with the link. That way you at least get traction from where you posted it (Twitter, Slack). If anyone was interested they'll click to see more. You mostly need:

- **Blog post title:** How we improved our app -----> How we reduced our app size by 30%.  
- **Blog's description/subtitle:** I'll discuss some tips and tricks -----> I'll discuss how creating a dSYM can cause bloat in your app and how stripping will remove it.  
- **Your message on Twitter:**  I've always struggled with what affects app size -----> Use `nm -a <your binary> | grep - | wc -l` to find out the number of debug symbols your binary has. Learn more in this blog post: [link] (OR: Discover the secrets in this article: [link])

## Other notes

- Add your website's URL at the bottom of every image you've created. Helps your branding and gets you more clicks when the image is shared outside your post 
- Be wary about accidentally publishing a post. This is why it's good to check your website every once a while and not just the homepage, but go through all your posts to make sure no post has actually sneaked in. Often an old post will get in because you removed the `draft: true` field but your post `date` field was from 6 months ago. So it appears in your list of posts, but not in the front page because you have a lot of newer pages. 
- Do I need to make a Pull Request for every new post?
  - If it's just a new post or some edit then just add a good commit message and push.
  - Is it a blog structural change? A Theme change? A CSS change? Something that took a while for you to figure out? Something totally different? Then yes. Add a pull request with a decent PR description and then review it. For anything you're not sure, then try to use the Neflify Previews.