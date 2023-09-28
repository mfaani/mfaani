---
title: "Everything I Learned About Blogging With Hugo"
date: 2022-04-23T04:17:58-04:00
category: 'content-creation'
draft: true
tags: ["hugo", "content-creation", "blogging"]
---

I started blogging in Dec 2021. It's been a wonderful journey. Has made able to gather my thoughts in a far more structured way. This post contains things I learned in terms of Hugo knowledge, how to write a blog post and more.

## Content Creation

- **Create drafts asap** i.e. when you have an idea or started learning something. I've forgotten key details days after let alone months down the road. Don't just write bullet points, jot down anything and everything. Often when I come back to my bullet points: "css variables", it's been 6 months since. I've forgotten everything I've learned. At other times simply because I have my attention more focused on some new challenge, I think my previous things has less important. Content in your brain is like fresh food. If you don't eat when it's warm and fresh then it will go bad or just take a lot of time to defrost, or some more fresh food will come. Most things after they are learned become trivial and you think less of them. Truth is, anything can be fresh for the right person. 
- **Use headers** from the beginning. Don't let your post turn into a graveyard.
- **Links can die**, ike Apple can remove a WWDC video. Make sure you annotate the link as much as possible. Example, don't just share the link to the session. Share the link and the name, year and minute of the session. That way folks can perhaps google it and find it.
- **Don't over explain.** If you're doing that, then usually it means you haven't found the right break down, wording, story, image to explain things. Or at least don't over explain too many things in a single blog post, unless your post is meant to cover a-z or be a post on the terminology of something. Small highly focused posts are often better. Some good examples of that are https://www.hackingwithswift.com and http://css-tricks.com. Most of the time they're focused. Maybe just go deep for a single subject. Not 5 at once. Or if someone has already done it, then you don't need to re-say it. Just link to theirs. tldr it's hard to have breadth and depth in the same post. 
  - Come back to the post in 2 days or 2 weeks and try to break it down again.
  - Instead of writing one gigantic post >> write five small posts on different concepts. Then write a sixth post that summarizes and pieces it all together.
  - Often when I split a single post into multiple, I can rationalize about the post a lot easier, because our brains can only cache a certain amount of context at once.
  - The key to being able to split it, is to to have good headers.
- **Use rich media.** Post videos/gifs as well. They make your website **rich**. Example, nstead of a screenshot of Apple's Playground, actually take a short video and demo it. Videos have audio, show UX far easier, quicker. You don't need to use them in every post, but just that you shouldn't shy away from them. 

## Benefits of getting early feedback 

- Share a [Netlify preview](https://docs.netlify.com/site-deploys/deploy-previews/) of your post with others when possible. It's a really easy way to get help from a group of experts in a Tweet or in Slack. Often typos, but a lot of time people will come back to you and be like:
  - "Your message in your post isn't clear, you're talking about X, then suddenly jump to Y"
  - "This line is just wrong. You've misundertood what *Foo* means. That's what *Bar* is"
  - "Use this documentation, blog post, other slack thread" to improve/deepen on your post.
- Often if you publish and then ask, people would be less caring. Previews are inticing much like how an early preview of a movie is. It makes the readers feel special. They truly are.

## How can you make sure you don't have a knowledge curse and things are clearly explained?
Read your blog posts again after a month or two after its published and see if everything still makes sense. 
I just came back to two of my old posts. I actually totally forgot I had them written. It's a peculiar feeling. I found: 
  - typos and grammatical mistakes. 
  - I found things that I skipped without explaining them. 
  - I found ways to explain something better. 
Basically, the context of a fresh mind (who might have forgotten some things) is closer to that of a reader with a blank slate — while the context of an author's mind is like one on steroids full of knowledge and context.
- Once you've published, then check all your links and images. Checking your links is important because it's not something you can figure out by reading the markdown. You have to render it.   

## Do I need to make a pull request for each post?
- If it's a new post? No. Just add a good commit message
- Is it a blog structural change? A Theme change? A CSS change? Something that took a while for you to figure out? Something totally different? Then yes. Add a PR with a decent PR description.

## Marketing your posts

- Instead of just sharing a link or what originated, share something with meat from your post and just add a link. That way you at least get traction from where you posted it (Twitter, Slack). If anyone was interested they'll click to see more. You mostly need:

**Blog post title:** How we improved our app >> better >> How we reduced our app size by 30% 

**Blog's description/subtitle:** I'll discuss some tips and tricks >> better >> I'll discuss how creating a dSYM can cause bloat in your app and how stripping will remove it.
**Your message on Twitter:**  I've always struggled with what affects app size >> better>> Use `nm -a <your binary> | grep - | wc -l` to find out the number of debug symbols your binary has. Learn more in this blog post: [link] (OR: Discover the secrets in this article: [link])


## Other notes

- Add your website's URL at the bottom of every image you've created. Helps your branding and gets you more clicks when the image is shared outside your post 
- Be wary about accidentally publishing a post. This is why it's good to check your website every once a while and not just the homepage, but go through all your posts to make sure no post has actually sneaked in. Often you miss 

