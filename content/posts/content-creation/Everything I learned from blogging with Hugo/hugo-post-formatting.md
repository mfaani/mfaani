---
title: "Hugo Post Formatting Basics"
date: 2022-04-23T04:17:58-04:00
category: 'content-creation'
draft: true
tags: ["hugo", "content-creation", "Hugo Formatting", "Hugo Frontmatter"]
---

- Add `ShowToc: true` to your frontmatter to show table of content. Your theme my not have the `ShotToc` frontmatter
- Customize `Description`. The value of this field is used as an abstract in your front page and link previews.
- Add two spaces to create new line after a line end. Otherwise hugo will just continue the line. This is something hard to grasp. Because of this I usually manually review my rendered post with `hugo server -D`

- Highlighting individual code lines. Example:
```swift { hl_lines=["16-20"]} 
class Foo {
    var name = "Jack"
}

```

or I can do: 

```bash {linenos=true linenostart=1}
rvm use ruby-2.5.1 || rvm install ruby-2.5.1
gem install bundler -v "2.3.3" 
bundle install
pod lib lint
```

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

- `.Title` at the top level of a layout page means website's title. But if nested within the context of a blog post, then `.Title` means the posts's title. See [here](https://youtu.be/w6_cQsTwd3Q?t=412) for more.
- Have expandable section. You can default it to open. See [here](https://gist.github.com/pierrejoubert73/902cc94d79424356a8d20be2b382e1ab). Example:

```
<details>
  <summary>See answer</summary>
  - in-order traversal for all will be: `[1,2,3,4]`
  - level-order would be different for each. 
</details>
```
- Shortcodes
## Hugo Theme

- git submodule

- The timestamp of your posts is important. Like you may have actually started your draft 10 days ago, or might have even started the draft before a currently published post. Because of that you should adjust your current timestamp to the publish date...