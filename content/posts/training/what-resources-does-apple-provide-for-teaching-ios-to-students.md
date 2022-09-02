---
title: "What Resources Does Apple Provide for teaching iOS to students?"
date: 2022-08-03T13:45:52-04:00
category: "training"
tags: ["iOS", "iOS-trainings", "swift-playgrounds", "teaching", "Apple Teacher Program"]
---

I've started an iOS training course in my local community. At the beginning I thought it would be easy for me to just start training people with whatever I know, then I was like let me see what are some of the resources out there. 


I found great amount of material prepared by Apple. Only problem was that there were soo many that I got confused. In this article I plan on cataloging the different resources **provided by Apple**. 

What you must understand is that some of Apple's Documents (PDFs) are out of sync with their Playground tutorials. Or some of their books found through the Books app are available in Xcode 11, Xcode 12, and Xcode 13. It's confusing because you may not notice that the book is for an older Xcode version. Pro-tip: If you see multiple books with the same name, try digging in and find your appropriate Xcode version.

### Where to begin?
Overall Apple has teaching material in their Swift Playgrounds app and PDF books. It just really depends on how you answer the following questions. 
- Are you looking for ages 10+ or 14+?
- Do you want to learn using Playgrounds or learn using PDF + Xcode?
- Do you want to learn SwiftUI or UIKit?
- Do you want to learn basic programming (for loops, conditions, etc) or want to jump in straight into building apps?

Overall Apple has two distinct series: 

| Name       | Age | Tutorial type | Swift vs. UIKit | Learn Basics vs. jump into building apps |
| -----------| ----| ------------- | --------------- | ---------------------------------------- |
| Everyone Can Code      | 10+       | Very engaging games for learning basic programmings | The SwiftUI portion is not gamified, but still interactive | Very basic concepts|
| Develop in Swift   | 14+        | Not interactive. Must just read PDF and follow | More tailored towards building apps + some content for discussing real world problems | Build multiple mini apps |


## Everyone Can Code
- [Everyone Can Code - Curriculum Guide](https://www.apple.com/education/docs/everyone-can-code-curriculum-guide.pdf)
![Every Can Code - Curriculum Guide](/everyone-can-code-curriculum.png "ATTENTION: This PDF isn't up to date")

### Playgrounds overview
![Playgrounds overview](/playgrounds-overview.png)

#### Problems within Playgrounds: 
- Can't search for text within it
- Can't command click to go to the definition of a type. 
## Playground Tutorials

### Learn to Code 1
**My Score: 10**

The Playground is focused on: 
- Commands: `moveForward()`
- Functions: `add(5, to: 10)`
- For Loops: `for i in 1...number {}` 
- Conditional code: `if lightIsGreen`
- Logical Operators: `if isBlocked && isOnGem`
- While Loops: `while ageIsUnder18()`
- Algorithms: functions and loops combined...

#### Teaching Tips: 

- Bring kids to the front or ask them to share their screen while they're coding. I had students who didn't want to go the first time, but after trying it they were asking to back for the 2nd time. 
- Take the pace as slow as possible. Understanding concepts is one thing. Understanding the syntax is another thing. I had a knowledge curse (Expert blindness) and thought I could finish the 'Learn to code 1' tutorial in 2 session. It's taking about 4 sessions. I forgot how I was slow in learning syntax myself too. 
- You can skip chapters and jump to whatever you want. (The SwiftUI playgrounds don't allows random access though.)
- Start simple. Ex if you have an assignment asking them to write a function then first begin by giving them the function signature and ask them to just implement it. Often figuring out the function signature is a struggle of its own. 
    - Having a Slack workspace can help with fast easy sharing of code snippets. Kids are super fast to joining your Slack. 
- Or ask questions that makes them think: 
    - What happens if I remove the double quotes?
    - Can you assign an `Int` to this (`String`) variable?
    - Would the function take a different variable? Example, you can pass a different constant, case. Example change `scaleToFit` to `scaleToFill`. 
    - Where did this image come from? This questions asks them to think bigger than the only file they're currently seeing i.e. see the project...

#### Chapters

![Learn to Code 1 - Chapters](/Learn-to-code-1-chapters.png)

### Learn to Code 2
**My Score: 10**

The Playground is focused on:
- Variables: `var age = 0`
- Types: Features are called properties. Behaviors are called methods. Dot Notation
- Initialization. Creating the new. `let expert = Expert()`
- Parameters. Creating options: `func paintRoom(color: Color)`
- World Building. Instead of playing the game, you learn how to create the scene by adding gems and bricks. 
- Arrays. Create a list. Your list has _methods_ to remove, append, insert or iterate over its elements. 

#### Everyone can Code - Puzzles
TBD
#### Everyone can Code - Adventures
TBD

### Get Started with Apps
**My Score: 8 (Because the playgrounds often crash. Otherwise they're great)**

- 💡 To circumvent the crashing, copy the playground as you make progress. Then delete the previous copy you had.  
- You'll learn how to declare views, place images and text, deploy useful modifiers, and understand the power of composability.   
- This isn't your typical playground. It's more of a mini-Xcode within Playgrounds.  
- You can't skip chapters. You have to go through it sequentially.  
- There are no undo buttons. If you delete a file, there's no undo button. I had to download a copy of the playground again, save the file I deleted into Files, then add the file into my original playground again. 

#### Rock Paper Scissor 
My Score: 6. Most of the game is already coded. You end up tweaking very few parts of it. 

#### Shapes
TBD
#### Get Started with Apps
TBD
#### Keep Going with Apps
TBD

### Solutions
https://education-static.apple.com/teaching-code/learn-to-code-solutions.pdf

## Teacher Guides
- Apple has courses that you can take as a teacher. You can become certified if you go through their courses. 
I tried on becoming a certified teacher for coding, but you first have to become certified in iPad or Mac training, then you can become certified for coding 🤷‍♂️.
To become certified for iPad, you have to go through the following tutorials: 
- the iPad itself
- Pages for iPad
- Keynote for iPad
- Numbers for iPad
- iMovie for iPad
- GarageBand for iPad
From https://appleteacher.apple.com/#/home/resources search for 'Playgrounds', 'Swift', 'Code'. 

I must admit I learned a number of things in these tutorials. They're relatively short. Takes about 3hrs to go through it all. 

- Apple also has a 'Teacher Guide' for its [Every Can Code Puzzles](https://books.apple.com/us/book/everyone-can-code-puzzles-teacher-guide/id1481279144) & [Everyone Can Code Adventures](https://books.apple.com/us/book/everyone-can-code-adventures-teacher-guide/id1514850776) tutorials. They're available to download from iBooks app. They have really good recommendations for what to say during the class, lots of nice images and (graphical) solutions.


## Develop in Swift series
- Find its Curriculum Guide from [here](https://www.apple.com/education/docs/develop-in-swift-curriculum-guide.pdf).
- Level: **high school** students (14+)
- Books contain Playgrounds & Template Xcode Projects that are used in different units. Books also contain 10-20 second videos for certain instructions. 
- Does NOT have the visual richness of the Playground tutorials. It's mainly because you're coding in Xcode, not Playgrounds.
- Does not teach using SwiftUI, Observables.
- Content: It has different books. That are meant to be read in the following order: 
    - Explorations
    - Fundamentals
    - Data Collections


In this post, I'll only illustrate the content within the _Explorations_ book. 

### Explorations
- Explorations (180hrs)
    - Unit 1: Values (Build a PhotoFrame App - Students get started with Xcode and playgrounds. They learn about the fundamental units of programming—the values that flow through their code, including text and numbers. They explore how to associate names with values using variables and constants, and how values can be used in simulations. They apply their skills to build a word game in a playground. The unit culminates in an app project to display a photo, which introduces them to the basics of Xcode and Interface Builder—tools they’ll use later for more complex projects.)
![PhotoFrame App](/PhotoFrame-app.png "PhotoFrame App")
    - Episode 1: The TV Club (Students will learn how searching on the web and signing up for accounts can impact their personal information and how to think about their privacy while using apps.)
    - Unit 2: Algorithms (Build a QuestionBot - Students learn about the fundamentals of structured code. They explore how functions can encapsulate repetitive tasks and how to use if/else statements to make decisions in their code. They also learn how Swift uses types to distinguish different kinds of data. They apply their skills in playgrounds to create a dancing BoogieBot and a data visualizer. The culminating project is a QuestionBot app that responds to user input from the keyboard.)
![QuestionBot App](/QuestionBot-app.png "QuestionBot App")
    - Episode 2: The Viewing Party (Students learn how data is represented inside their devices and how it flows across the internet. They also learn more about security and data privacy)
    - Unit 3: Organizing Data (Build a BouncyBall App - Students explore how to create custom types using structs, group large quantities of items into arrays, and process them using loops. They also learn how to use enums to represent a set of related values. They apply their knowledge by creating pixel art, extending their data visualizer, and creating an algorithm to determine password security. In the app project at the end of the unit, they build an interactive game app with colorful shapes.)
    ![BouncingBall App](/BouncingBall-app.png "BouncingBall App")
    - Episode 3: Sharing Photos (Students explore how sharing data online can impact privacy and result in other unanticipated consequences. They also look at how images are captured and processed by a mobile device and how a social media service handles posts) 
    - Unit 4: Building Apps (Build an ElementQuiz App - Students deepen their skills in Xcode and Interface Builder in guided projects that show how to build an app from the ground up. Students use techniques to add user interface elements to a screen, connect those elements to their code, and respond to the events generated by user interaction. They also experience the incremental development of an app by gradually expanding its capabilities, testing it at each phase.)

The book is divided into four **units** that focus on building your programming skills and understanding of key concepts. Between units, you’ll explore a story about a group of students in a TV club. As you follow these **episodes**, you’ll have a chance to think about different aspects of online engagement, how information is shared online, and what kinds of decisions need to be made in a connected world.
By the end of the book, you will have built several simple apps and prototyped an app of your own design. And along the way, you’ll have gotten a taste for the world of app development—gaining an appreciation for how technology works, how you can use it to express yourself, and how it can be used to solve problems.
Each unit is divided into four sections:
- Get Started
- Play
- Build
- Design

### Fundamentals (180hrs)
- Unit 1: Getting Started with App Development 
- Unit 2: Introduction to **UIKit**
- Unit 3: Navigation and Workflows
### Data Collections (180hrs)
- Unit 1: Tables and Persistence 
- Unit 2: Working with the Web 
- Unit 3: Advanced Data Display

There's also a 4th book named: "AP CS Principles". The AP® CS Principles course extends _Develop in Swift Explorations_ to prepare students for the AP® Computer Science Principles exam.

## How to start a coding club

Tips from [Swift Code Club](https://www.apple.com/education/docs/swift-club-xcode.pdf) on how to start a coding club:
- Have posters and T-Shirts created for the club.
- Make your first week a big event.
- Add friendly competitions.
- Recruit judges and mentors. Bring in a guest speaker. 
- Show videos, etc. 
- Record showcases and presentations. 
- Allow people to pitch app ideas and showcase prototypes and then give feedback.


### Other Tutorials
https://developer.apple.com/tutorials/swiftui


## Summary
- If you have absolute beginners or under 14yrs of age then you can only use the 'Everyone can code' Playground series. 
- If you have 14+ years of age students and want to start with more real world code and are also ok with learning UIKit then use 'Develop in Swift' series. 
- If you're not ok with teaching UIKit and want to only teach SwiftUI then you can't use 'Develop in Swift'. Must use the various Playgrounds Apple has. 

