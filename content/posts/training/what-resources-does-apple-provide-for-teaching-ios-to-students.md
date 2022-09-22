---
title: "What Resources Does Apple Provide for teaching iOS to students?"
date: 2022-09-21T13:45:52-04:00
description: "Buried deep under Apple's K-12 resources?"
category: "training"
tags: ["iOS", "iOS-trainings", "swift-playgrounds", "teaching", "Apple Teacher Program", "K-12 Coding"]
---

Note: This post has got a bit longer than what I originally intended. You might want to skip to the very end to see the [verdict](http://mfaani.com/posts/training/what-resources-does-apple-provide-for-teaching-ios-to-students/#verdict---what-course-should-i-take) and [table](http://mfaani.com/posts/training/what-resources-does-apple-provide-for-teaching-ios-to-students/#where-to-begin) first and then read again.

I've started an iOS training course in my local community. At the beginning I thought it would be easy for me to just start training people with whatever I know. Later I decided to look at some available resources. I found great amount of material prepared by Apple. Only problem was that there were soo many that I got confused. In this article I plan on cataloging and comparing the different resources **provided by Apple**. 

What you must understand is:
- Some of Apple's Documents (PDFs) are a tiny bit out sync with their Playground tutorials. 
- Some of their books found through the Books app are available in Xcode 11, Xcode 12, and Xcode 13. Making it confusing because you may not notice that the book is for an older Xcode version. So just make sure you're downloading the correct Xcode version. 
- Some Playgrounds require a (free) subscription. 

## Where to begin?
Overall Apple has teaching material in their Swift Playgrounds app and PDF books. In my opinion what you choose, depends on how you answer the following questions. 
- Are you looking for ages 10+ or 14+?
- Do you want to learn using Playgrounds or learn using PDF + Xcode?
- Do you want to learn SwiftUI or UIKit?
- Do you want to learn basic programming (for loops, conditions, etc) or want to jump in straight into building apps?

Overall Apple has two distinct series. 'Every Can Code' & 'Develop in Swift'

| Book Name      | Associated Playgrounds | Tutorial type | Swift vs. UIKit |  Real world vs Abstract concepts |
| -----------    | -----------------------| ------------- | --------------- |  ------------------------------- |
| Everyone Can Code **Puzzles** (Age: 10+) | Learn to Code 1, Learn to Code 2, Spirals, 'Rock, Paper, Scissors' and more|Very engaging games with awesome graphics and cute characters for learning basic programmings | The SwiftUI portion is not gamified, but still interactive | It's just concepts or simple views | 
| Everyone Can Code **Adventures** (Age: 12+) | Learn to Code 2, Blu's Adventure, 'Lights, Camera, Code', Assemble your camera, Flashy Photos and more | Very engaging games with awesome graphics and cute characters for learning basic programmings | The SwiftUI portion is not gamified, but still interactive | More advance concepts and simple views | 
| Develop in Swift (Age: 14+) | All Xcode, No Playgrounds | Not interactive. Must just read PDF and follow in Xcode | UIKit based. CompletionHandler based | You build apps and learn about the app design process + discussions about real programmer's problems and considerations|

In this post I focus on going through the 'Everyone can code' series. I have another post in the works for 'Develop in Swift'. 

# Overview 
![Everyone Can Code - Curriculum Guide](/everyone-can-code-curriculum.png "ATTENTION: This PDF isn't 100% updated.")
[Everyone Can Code - Curriculum Guide](https://www.apple.com/education/docs/everyone-can-code-curriculum-guide.pdf)

### Playgrounds overview
![Playgrounds overview](/playgrounds-overview.png)

## Everyone Can Code Puzzles
### ebook: [Everyone can code Puzzles](https://books.apple.com/us/book/everyone-can-code-puzzles/id1481279769)  
- Score: 10
The book is amazingly rich with its content, instructions and graphics. 
![Everyone can code Puzzles Playgrounds](/everyone-can-code-puzzles.png)


### Playground - Learn to Code 1
- Score: 9
    - Only reason I gave a 9 is because it's often too difficult — even for myself. The hints also aren't that great. In the while loop, and algorithm chapters I had to look into the solutions myself. Imagine how difficult they must be. 
    - Otherwise the graphics are rich. The music and sound are top-notch. The pace and direction of the lessons are generally fantastic. And each lesson/chapter is very focused on learning one thing only — while using what you learned from previous 
    
- Chapters
    - Commands: `moveForward()`
    - Functions: `add(5, to: 10)`
    - For Loops: `for i in 1...number {}` 
    - Conditional code: `if lightIsGreen`
    - Logical Operators: `if isBlocked && isOnGem`
    - While Loops: `while ageIsUnder18()`
    - Algorithms: functions and loops combined...

The Playground itself should suffice, but if you wanted extra depth and breadth then you can use the 'Everyone Can Code Puzzles' ebook.
###  Playground - Rock Paper Scissor 
**My Score: 6**  
Most of the game is already coded. You end up tweaking very few parts of it. As a result you don't learn as much. 

### Playground -  MeeBot Dances†
**My score:** TBD  
†: You need to **subscribe** UBTech Jimu Robots:

> MeeBot Dances: In the “From Other Publishers” section of the More Playgrounds screen, tap UBTech Jimu Robots, then tap Subscribe.  
Tap Get to download the MeeBot 2.0 Dances playground.

I wasn't able to find the correct URL for subscription. You can just download their playground from [here](https://www.apple.com/newsroom/2017/06/swift-playgrounds-expands-coding-education-to-robots-drones-and-musical-instruments/).  
It's not a requirement but you can hook up your iPad to a real robot and then start coding. The robot can be purchased from [here](https://www.amazon.com/UBTECH-Jimu-Robot-BuzzBot-MuttBot/dp/B01LXZE4MT).

###  Playgrounds - Shapes, Answers, Spirals
TBD

###  Playground - Learn to Code 2
**My Score: 10**

The Playground is focused on:
- Variables: `var age = 0`
- Types: Features are called properties. Behaviors are called methods. Dot Notation
- Initialization. Creating the new. `let expert = Expert()`
- Parameters. Creating options: `func paintRoom(color: Color)`
- World Building. Instead of playing the game, you learn how to create the scene by adding gems and bricks. 
- Arrays. Create a list. Your list has _methods_ to remove, append, insert or iterate over its elements. 
## Everyone can Code - Adventures
[Every can code Adventures](https://books.apple.com/us/book/everyone-can-code-adventures/id1514850778)
- Score: 10
It's exceptionally good as the previous book, but then goes into much more depth. 
![Everyone can code Puzzles Adventures](/everyone-can-code-adventures.png)

- Chapters
    - Objects in Views `scene.place(ghost, at: Point(x:250, y:250))`
    - Events and Handlers: `toolName.onTouchMoved = handlerName(touch: )`
    - Arrays: `images.remove(at: Int)`
    - More Events and Handlers: `let xDirection = touch.position.x – player.position.x`
    - Functions as arguments: 

        ```
        scene.camera.when(snail, isWithin: 10.cm, do: {
        snail.flee(from: scene.camera.node, safeDistance: 100.cm)
        })
        ```
    - Return Types and Outputs: `return graphic`
    - Classes and Components: 
    ```
    public func start() {
		cameraView.start()
	} 
    ```


###  Playground - Get Started with Apps
**My Score: 8 (Because the playgrounds often crash. Otherwise they're great)**
It crashes in the way that you can't open the playground anymore 😢. Even a hard reset didn't fix things. 
- 💡 To circumvent the crashing, copy the playground as you make progress. Then delete the previous copy you had.  
- You'll learn how to declare views, place images and text, deploy useful modifiers, and understand the power of composability.   
- This isn't your typical playground. It's more of a mini-Xcode within Playgrounds.  
- You can't skip chapters. You have to go through it sequentially.  
- There are no undo buttons. If you delete a file, there's no undo button. I had to download a copy of the playground again, save the file I deleted into Files, then add the file into my original playground again. 

#### Get Started with Apps, Keep Going with Apps
TBD

### Solutions
https://education-static.apple.com/teaching-code/learn-to-code-solutions.pdf

## Teacher Guides

### Teacher courses
- Apple has courses that you can take as a teacher. You can become certified if you go through their courses. 
I tried on becoming a certified teacher for coding, but you first have to become certified in iPad or Mac training, then you can become certified for coding 🤷‍♂️.
To become certified for iPad, you have to go through the following tutorials: 
- The iPad itself
- Pages for iPad
- Keynote for iPad
- Numbers for iPad
- iMovie for iPad
- GarageBand for iPad
From https://appleteacher.apple.com/#/home/resources search for 'Playgrounds', 'Swift', 'Code'. 

I must admit I learned a number of things in these tutorials. They're relatively short. Takes about 3hrs to go through it all. The GarageBand tutorial was **very difficult**.

You can also get certified for Mac, and a number of other courses. See the rest of the courses:

![Apple course badges](/apple-teaching-course-badges.png)


### Teacher guides
- Apple also has a 'Teacher Guide' for its [Every Can Code Puzzles](https://books.apple.com/us/book/everyone-can-code-puzzles-teacher-guide/id1481279144) & [Everyone Can Code Adventures](https://books.apple.com/us/book/everyone-can-code-adventures-teacher-guide/id1514850776) tutorials. They're available to download from iBooks app. They have really good recommendations for what to say during the class, lots of nice images and (graphical) solutions.

#### Teacher forums
Also since late Aug 2022, Apple has launched forums: https://education.apple.com/en/community

#### How to start a coding club

Tips from [Swift Code Club](https://www.apple.com/education/docs/swift-club-xcode.pdf) on how to start a coding club:
- Have posters and T-Shirts created for the club.
- Make your first week a big event.
- Add friendly competitions.
- Recruit judges and mentors. Bring in a guest speaker. 
- Show videos, etc. 
- Record showcases and presentations. 
- Allow people to pitch app ideas and showcase prototypes and then give feedback.

### General Teaching Tips: 

- Bring kids to the front or ask them to share their screen while they're coding. I had students who didn't want to go the first time, but after trying it they were asking to back for the 2nd time. 
- Take the pace as slow as possible. Understanding concepts is one thing. Understanding the syntax is another thing. I had a knowledge curse (Expert blindness) and thought I could finish the 'Learn to code 1' tutorial in 2 session. It's taking about 4 sessions, but we still don't cover all lessons of every chapter. I forgot how I was slow in learning syntax myself too. 
- You can skip chapters and jump to whatever you want. (The SwiftUI playgrounds don't allows random access though.)
- Start simple. Ex if you have an assignment asking them to write a function then first begin by giving them the function signature and ask them to just implement it. Figuring out the function signature is a struggle of its own. 
- Having a Slack workspace can help with fast easy sharing of code snippets. Kids are super fast to joining your Slack. 
- Or ask questions that makes them think: 
    - What happens if I remove the double quotes?
    - Can you assign an `Int` to this (`String`) variable?
    - Would the function take a different variable? Example, you can pass a different constant, case. Example change `scaleToFit` to `scaleToFill`. 
    - Where did this image come from? This questions asks them to think bigger than the only file they're currently seeing i.e. see the project...

### Other Tutorials
https://developer.apple.com/tutorials/swiftui

## Verdict - what course should I take?
- If you have absolute beginners or under 14yrs of age then you can only use the 'Everyone can code' Playground series. 
    - If you want extra depth, read the ebooks while you do the Playgrounds. Otherwise just go with the Playgrounds. 
    - Regardless of using the ebooks during class, the ebooks are great resource for a student to have. 
- If you have 14+ years of age students and want to start with more real world code and are also ok with learning UIKit then use 'Develop in Swift' series. Because UIKit will be useful and around in a lot of big companies until 2030...
- If you're not ok with teaching UIKit and want to only teach SwiftUI then you can't use 'Develop in Swift'. Must use the various Playgrounds Apple has. 

