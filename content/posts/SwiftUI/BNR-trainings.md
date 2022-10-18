---
title: "BNR Trainings"
date: 2022-10-11T09:52:21-04:00
draft: true
---

- add a border modifier around your views (with different colors) to see how they each look like. 
- a simple `if else` will work for _handling_ a state. However it's not sufficient for switching/toggling through user experience. In that case you need a Binding/State. 
    - SwiftUI uses `@State` to know when to refresh a view. It monitors the value of that. To automatically update the view. 
    - Benefits of using Bindings: 
        - You no longer need to update the value yourself. 
        - When it changes, your view will change. 
- print statements, breakpoints don't work in SwiftUI Previews. 
- When you toggle something in your state, your entire body will be re-rendered ALL over again. If you placed a breakpoint again then your **entire body** will be re-rendered again. Even stuff that aren't associated with the change. 
    - When we do `VStack { ... }` we're _initializing_ a `VStack` i.e. if our body had a VStack within it, we're re-initializing it all again. 
    - In SwiftUI the lifecycle is very different. In UIView, are reference types. They're allocated on the heap with a memory address. They exist as long as the view is on the screen. In SwiftUI, are views are more like descriptions/blueprints of the view. Whenever the SwiftUI wants to re-draw, it will re-draw everything again. It will be like, lemme compare that with the previous blueprint/state. 
- You can easily create a color picker using `ColorPicker` 
- Project >> Assets >> Accent Color. Example the Notes Accent color is yellow. Help/Calendar is Red. This effects the coloring for the entire view hierarchy. 
- `Text` instances aren't tint-able. `Buttons` are tinted. 
## SwiftUI vs UIKit
    - There's no controller. Any behavior onAppear, onDisAppear on notification fire, all of it has to happen within the `body` property. This is good and bad. 
        - Bad: because that's a lot to put in a variable. So it's always great to break views. Much more so that we did in UIKit. In UIKit there's a small cost in breaking views, because there's an allocation on the heap. In SwiftUI all our views blue-print based. ????? What is the view itself ultimately based off of? 
        - 
    - Declarative, I'll declare how it looks, but won't dictate how it should do it. You declare that you want peanut butter and strawberry jelly + wheat bread. But you don't say how to do it. You let the chef make the sandwich in whatever order they want to make it. And with this in our mind, we let every different device, architecture (example: macOS vs. iOS vs. iPadOS). Another way of thinking of declartive: "you’re not really writing precise instructions to solve the problem; it’s more like you’re describing the problem in the language, and then it goes and solves it for you."
    - Reactive: React to changes in data and user interaction. 

## Modifiers
- order of modifiers matter. 
```
Text()
.padding
.border

vs.

Text()
.border
.padding
```

- in the first example, `.border` gets applied to the result of `Text.padding`
- in the second example, the `.padding` get applied to the result of the `Text.border`
![](screenshot.png)

## View Composition
- `some View`, means an Opaque type. 
- as soon as we apply a viewmodifier to a Text, it will become a `ModifiedContent` type i.e. it's no longer a pure `Text` view. 
This is why we have to return `some View`. 
SCREENSHOT
- `VStack<TupleView<Text, Text>>


## ViewBuilder restrictions?
- With `VStack` or all other `body` variables, we have `ViewBuilder`s and that allows us to do just do: 

```
VStack {
    // Only views
    Text(...)
    Text(...)

    // Only variables
    let x = 2

    // Only conditions
    if else

    // other stuff that aren't allowed. Will cause errors
    print("foo")
    
}

```
Similarly we just do 

```
Text
Text
```

i.e. we don't have to do 
```
return [Text(...), 
Text(...)]
```

There's a way to get out of this. That is to do: 

```
print("....")

return Text(...)
```

The explicit mention of `return` does that. But you're no longer using `ViewBuilder`s any more. 

Or you could also just do 

`let _ = print("sth")`

and just because it's a variable now things just work...


`ViewBuilder` uses `ResultBuilder` 
This year Apple introduced a `RegexBuilder` another type of a _Result Builder_ 

let's create a `@resultBuilder`

```
@resultBuilder
struct StringBuilder {

}
``` 
won't compile. 

Needs a `buildBlock` function 

SCREENSHOT

SCREENSHOT2,3

### How to hack your own `print(_ value: Any)` function to be able to chain in a print statement...

```
extension View { 
    func print(_ value: Any) -> some View {
        Swift.print(value)
        return self
    }
}
```
👆 Not using ViewBuilder, we're just doing `return` ...

```
extension View {
    func dumpViewType() -> some View { 
    Swift.print(Mirror(reflecting: self))
    return self
    }
}
```

If you notice the trick in the two above functions was to call return `self` itself. So you can 

## View Layout - How views get sizes and positioned

UIKit, we have views, buttons, windows. We have some containers and add subviews. That's how we layer content. 

In SU the default spacing between different types are different. 

### Sizing
- Parent propose size to its children. All that's possible 
- Children return their actual size
- Based upon the `alignment` & `spacing` the item size of every child the items get positioned. 
    - default alignment is being centered. 
    - Text just take only the space it needs
    - `RounderRectangle` on the other hand, takes the _entire_ space it needs...
- Stackviews are slightly different. They have divide their space into identical sizes


### Why are some items greedy? 
Simple answer: It's because it wants to be. 
i.e. the parent passes a proposed size. The child then decides based on its on intrinsic content size or whatever sort inherent feature it has, what its size is suppose to be. 

In the example of `Text` the Text's internal sizing logic is to be just the size it needs. It just returns a `CGSize` that it just needs



### Layouts: 
- Completely fixed size `fixedSize`
    - Image (that hasn't be marked with `.resizable`)
    - Anything that isn't marked with `fixedSize`
    - Anything marked with `.frame(width: x, height: y)`
    - Note: you can a fixed size for one dimension e.g. I think `Divider`

- Partially fixed size (min, max)
- Completely flexible

Things get complicated when you have a mix of the above. 

If things get complex, then it starts looking for the (min, max) size for each view...

So if you have 

```
Image (200, 300)
Circle 
Text min: (20, 30) max: 
Circle
```

and have a height of 1000 and need 3 spacers of 10. So you start with 970
1. It will first fixed items
Then it will first take the full height of the image. 
so you're left with 770. 
2. Then it looks for partially fixed: 
It will take the min space for the text so now you're left with 750 space
3. It will look for fully flexible and split it in half for space. i.e. each have 375 space...

`maxWidth: .infinity` vs. `width: weight`

`maxWidth` is a suggestion. It won't cause warnings
`width` set to `infnity` is actually setting the width to infinity. That's will generate a warning. 



### Frame
It's not a property. It's a container view, just like padding i.e. we wrap it in a new view. 

```
.frame(width: 200, height: 300)
```

SCREENSHOT

```
WHITE VIEW              400 x 800
    Border View     proposed: 400 x 800 returned to (Border View)
        Frame View  proposed: 300 x 300 returned to (Border View) 300x300
            Text    proposed: 400 x 400 returned (to frameView ): 100x50

var body: some View {
    Text("Hi everyone")
    .frame(width: 300, height: 300)
    .border(.red)
}

```

Frame view doesn't care what size was proposed to it. It doesn't matter if its parent was less/more. Also doesn't care for what its child returns!!

Think of `frame` as a container. Not a stretcher...

** In UIKit **
You can draw outside the bounds of a view. That's how scrollview works. 
Clicking outside doesn't work

With SwiftUI, you can still interact outside its bound. While that's terrible UI. 

Docs:
> You can think of fixedSize() as the creation of a counter proposal to the view size proposed to a view by its parent. The ideal size of a view, and the specific effects of fixedSize() depends on the particular view and how you have configured it.

## Bindings
Problem binding solves is:

You want to communicate state / source of truth between two views:
1. if you just use `var`s` you can't mutate things within the `body`, since its a computed property. 

2. Having the need to pass in completionBlocks to just set values of updates is UIKit-ish way. 
It's not declarative like SwiftUI though. i.e. if I have to do `onIncrement: (Int) -> Void`

3. Solution. Use Bindings: 
Think of `Binding<T>` as read-write with a value from another struct.

## @State vs . @Binding

- Binding is never the source of truth. External data managed by another view.
- State is always the source of truth. Internal data managed by this view. -> Almost always you'd want this to be a `private` property. 


A ZStack will be as wide as the widest view and tall as the tallest view. 

The size that gets proposed to each modifier / element in your body, depends on where you pluck it in: 

```

var body: some View {
    ZStack {
    Color.blue
    }
}
```


### Color
It can be either: 
- A View
```
Text(...)
Color.red
```
- The actual color of the font a rectangular, etc. 
```
Text(...)
.foregroundColor(.red)
```

SCREENSHOT 
use `Color.accentColor` this will use the accentColor of your app. So for the camera app it's blue. On iOS that's the default accentColor.  

## Background vs Overlay
- Background is ZStack behind. Overlay is ZStack front
- Overlay is usually not in front of the entirety of the view. Example a checkmark for a selected image. 
    - For an overlay, we can move it around using `offset`, but that requires passing explicit values. And it won't work well for different screen sizes. 
    - The alternate is to use a `frame` modifier. The thing is, you want to respect the position of its parent. i.e. take in the size of the parent so you'd just do: `frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .bottomTrailing)`
SCREENSHOT 10:01am


## Tabs and Collections

```
Current State    New State
    A               O
    I               A
    O               I
                    O
```
We can't tell how we landed to the new state. It MIGHT be that:
- We inserted an `O` at the beginning.
- We removed `O` and added two new `O`s
- or some other transition. 

SwiftUI won't know how we transitioned from our current state to our new state. 
- It won't be able to know how it should animate the transition. 
- Even if we didn't care about the transition, often it's about maintaining the state 🤔

Because of this we need a _stable & unique ID_.
- Stable: It doesn't change.
- Unique: It's different between all rows. 

A great way to demonstrate the issue is using switches. Because switches should maintain their toggled state.

### IMPORTANT💡
If you just append/insert (Example: `books.append(book4)`) then the items in the array won't get recreated. That way you maintain your state. Because the UUID you used won't get recalculated. 
If you reset your entire array (Example: `books = newBooksArray`) then you've lost your previous state. UUID will get recalculated. 

Why can't I just do: 

```
for book in books {
    BookView(book: book)
}
```

- Because SwiftUI wants to enforce you to do use `ForEach(Sequence, id: )`
- This is intentional decision. Apple didn't build the `buildArray` method for the `ViewBuilder`. So it would force us to think about it. 

### Tabs
Unlike UIKit, in SwiftUI, there are TWO tab bars:

-  Tab Bar
- Paginated Pages

#### Tab Bar
Every item in the tab bar requires: 
- Name
- Image
- The view it should represent

```
TabView {
    HomeView()
        . tabItem {
            Label("Home", systemImage: ...)
        }
    SecurityView()
        . tabItem {
            Label("Security", image: ...)
        }
    MoreView()
        . tabItem {
            Label("More", systemImage: ...)
        }
}
```

### Label
Anything with a view + text. It's similar to UILabel, but not exactly. 


#### Pages
```
TabBar {
    ForEach(pages) {

    }
}
```

## Presentations
Slides: Dismissing Sheets

- Sheets. 
    - Good for temp tasks
    - Dismiss with drag

in SwiftUI views are a function of states, not a sequence of events 🤔. Meaning we need some data that dictates that. It's not just based on an event. Then we use a sheet modifier. 

```
.sheet(isPresented: $presentSheet) {
    SheetView()
}
```



SCREENSHOT 1:56:down: 

```
.sheet(isPresented: $presentSheet) {
    SheetView(isPresented: $presentSheet) 
}
```

presentSheet is a `Binding` because internally there's must be a variable to use to dismiss the sheet when dragging down. 
While this works, it's not the best way — because we have to constantly pass down `isPresented` 

### Alternate - Environment
- Font
- Line Limit
- Editing Mode
- Orientation
- Color mode (dark mode)

Environment values can be overridden at any given value. 

At any given time when we do `.font(.largeTitle)` we're actually overriding the Env value. 

#### Interesting
When we do `.font(.largeTitle)` on a `VStack`, it's much like updating the env value on that VStack i.e. as if we did: 

```
VStack {

}.environment(/.Font, Font(.largeTitle))
```

Changing the env is extremely helpful for previewing in differen orientations, looks, locale, etc. 

### Create your own env variables

You need to use EnvironmentKey

```swift
struct ThemeKey: EnvironmentKey {

}
```

```swift
extension View {
    func theme(_ theme: Theme) -> some View {
        self.environment(\.theme, theme)
    }
}
```

To present different kinds of sheets: 
SCREENSHOT 2:25

### How can I just show a view in the middle of the screen using presentations? 
- Maybe don't use presentations. Rather just use ZStacks. But ZStacks don't give you dismiss on other places out of the box.
- Presentations are for a different screen/state

### Sheet
You can either have a boolean to know when to show the sheet
or pass some other variable `Binding<Item>` example:
- from your table, you can do: 

```swift
@State var presentedBook: Book? 


sheet($presentedBook) { book in 
    BookView(book)
}
```

### PopOver
you can easily just do :

```swift
.popover(item: $presentedSheet) { sheet in 
    CustomView()
}
```

What's truly important is where you apply the `popOver`. If you apply it on the button view itself, then it will get positions relative to the popOver. 

## Environments vs Preferences
- Environments pass info down the hierarchy
- Preferences pass info up the hierarchy 
- Alternatively you can use a `class` type. That would help with sharing stuff. 


### Initialization vs call to body
If you initialize views that aren't shown then: 
- The `init` method will get called
- The body will only get called/processed when the view is shown. 

`onAppear`, `onDisappear` won't get called if it's the same ID. Because it's just getting moved. It's not getting re-rendered. 


The `init` method itself shouldn't have any side-effects. Because of the 👆

Would the VM/properties also get initialized every time it gets processed or is it just the `body`?!

When views get created in SU, it's either: 
- first time on screen
- already on screen. Just another update to its blueprint <-- this results in the `count` property to **retain** its value.


SCREENSHOT 3:53

SCREENSSHOT 9:23am
about changing the icon of the page item to use what we want example an magnifier. 

9:32SS

You can create your own button styles. You might want to get the Button's `Configuration`. The configuration will give you the label, and then you can adjust its font, opacity based on the button's state (isPressed, isSelected, etc. ) or based on the button's role (isDestructive)

## IMPORTANT

If I have the follow items 
```
current rows: 1       2       3       4(ondisappear will get during the transition, because it no longer exists)
new rows    :   3       6(onAppear)       1       2

```

The above will call `init` in 3, 6, 1, 2
However only the `body` will get called for 6. Because that was the only that was changed. 

If we go from: 

```
current rows: 1       2       3       
new rows    : 3       1       2
```

Then the init method of 3,1,2 will get called. `body` won't get called again though.


```
current rows: 1       2       3       
new rows    : 1       2       3 (property changed)
```

Then the init method of 3,1,2 will get called. `body` will only get called for 3, because the view requires an update. 

## List & Menu
- List is a container for rows/data.
- Menu is a container for buttons. 

Lists already have paddings. 
They start from the top, not center. Hence you don't need `Spacer()` at the bottom. 

```swift
ForEach(books, id: \.id) { book in
    BookView(book: book)
}
.onDelete { indexSet in 
    books.remove(atOffsets: indexSet)
}
```

If you don't have the `onDelete` then you won't get the option to swipe to left to delete the cell. It returns an array of indices — even though you get the callback one at a time. 
I suppose it's an array because it needs to support multi-row deletion on macOS. 

You can also add an `EditButton()`. This is built-in within SwiftUI. It passes an env variable. 🤔🤔🤔

Is it a good thing about SwiftUI that viewModifiers get exposed even in places where they shouldn't be?!
i.e. it shouldn't be exposed in this context...

Within your list, you have `Sections`. Each `Section` can have a `Header` or `Footer`, You can pluck in `Spacer` in between these to give custom spacing. 

10:48 

Clever way of scoping a function to certain views only. i.e. only those that conform to a certain protocol.

10:56 
`introspect` ?????????????


Anything not initialized within our view, is EXTERNAL. 

EXTERNAL
```
var company: Company
@Binding var company: Company
```

INTERNAL
```
var name = "Ali"
@State name = "Ali
```

12:09 ??????

Not sure why we needed to do Binding....


NEVER USE `\.self` for id — if the data is mutable. 
i.e. if the hash is based of String/Int that are mutable, then the hash would change as the result would change. 
This would signal to SwiftUI that it's a new View rather than just being a new render of the view. 
`\.self` is only good for enum cases that are static. 

## Group

1. 
If you put more than 10 views, then Xcode will throw an error. 
So you have throw some of the views under a `Group`.
`Group`, `HStack`, `VStack`, `Section` all group views together. However `Group` **doesn't** alter the layout. 

2.
Group is also useful if you have some modifiers that you want to apply to a number of elements...

```swift
Group {
    if isOn {
        Color.yellow
    } else {
        Color.black
    }
}
.opacity(0.5)
```

### Why do you need return for non-`body` named views? 


It's because every you're seeing: 

```swift
var body: some View {
    
}
```

It's actually 
```swift
@ViewBuilder var body: some View {
    
}
```

ViewBuilder:
- remove the need for using comas between views
- enforcing the necessity of `return` 
- It does NOT help with accepting `Text` or `Button` as two possible values of a `View` property. `some` is what makes that possible...

If our `ResultBuilder` was something like: 

```swift
struct myVStack {
    func buildContents<FirstView: View>(_ firstView: FirstView...) {

    }
}
```

👆That won't work. Because if `FirstView` is set to `Text` then the 2nd view in the `VStack` also has to be `Text`. It can't be `Button` or `List`, etc. 

## Objects with Persistence data
So far all the data is getting stored within the view. 


### ScenePhase
Informs us of the app state
- background
- inactive
- active

```swift
@Environment(\.scenePhase) var scene
...
ZStack {
    importantView()
    if scene !=active {
        Color.yellow // hides all the view
    }
}

However we rather not have to do this on every view. Either make this a modifier, or just do at the root level...

```

Thu - 3:24 Great MODIFIER
1. Create a `ViewModifier` by adopting the `ViewModifier` protocol. It takes in _existing_ content. Returns a `View`
2. create a function which applies the created modifier against the current view and returns a new modified view. Example. `self.modifier(PrivacyModifier())`


## ObservableObject

```swift
class PeopleStore: ObservableObject {
    var people: [Person] = [] {
        willSet {
            objectWillChange.send()
        }
    }
}
```

This is a lot of work. Like we have to write `willSet` for every property. 
And then observe a `PeopleStore` property with `ObservableObject` 

So instead you'd just do `@Published var people: [Person] = []`

## Dependency Challenge 

3:42

```swift
viewA                   viewB               viewC           viewD
                    ->              ->                ->                                  
var store: Store                                         var store: Store (needs to `store`)
```

```
struct PeopleView: View {
    @EnvironmentObject var store: Store

    var body: some View { ... }
}
```
### Problems with EnvironmentObjects
- While the above works. It's not ideal because it can lead to a crash if the property doesn't exist. 
- Anytime you change the `Font` env variable, you change the value down the path for all views. i.e. you **can only pass one** variable for a given type down the path. Solution is to use `ObservedObject` instead. 

Class types don’t work the same way.
If you had a Button to call the `load()` method, SwiftUI wouldn’t know that the allEntries changes as a result.
You must opt-in to tell the system about changes using the ObservableObject protocol.

### @Published vs objectWillChange.send()
- `@Published` is cleaner. 
- `objectWillChange.send()` gives the ability to do any customization. 

-------

Fri 9:15 (2nd image)
Lists, can have `.swipeActions`

```swift
.swipeActions { 
    Button("Hello")
}
```

```swift
.swipeActions(edge: .leading, 
allowsFullSwipe: true) {
    Button { } label: {
        Label("Favorite", systemImage: "star.fill")
    }
    .tint(Color.green)
}
```

## source of truth in VM or in View?
WHEREVER you initailize and saved/persist in your application, is where the state is owned. 

A VM, can't store a reference to a itself. 
A view that references the VM is actually storing a reference to that VM. 
Hence the view is the source of truth.

9:57

## How to add chevron to each item in the row? 
That implies you want to navigate. 
So just wrap your view with a `NavigationLink`



It's important to that for every navigation `destination`, the view will get initialized. 
HOWEVER the `body` won't get called. 
Things will work only if your `init` methods don't have side-effects. This is absolutely IMPORTANT. i.e. don't start a network request within your `init`. 

To add items to your NavBars, you should use `.toolBar` modifier. 

You can wrap each item in the toolbar within ` ToolBarItem` and then pass a placement to position it at the bottom or top of the screen. T

### Scaled Metric
11:35 aobout

can do: 

```swift
@ScaledMetric(relativeTo: .largeTitle) var size = 45.0

var fontSize: Double {
    min(70, size)
}

Text("hi poeple", font: )

```



## Interoperability

You need to be able to: 
- initialize `UIView`s — from SwiftUI
- alter/update the created UIView you just created — from SwiftUI

```swift
struct MyLabel: View, UIViewRepresentable {
    var text: String 

    func makeUIView(context: Context) -> UILabel {
        let label = UILabel()
        label.text = text

        return label
    }

    func updateUIView(_ uiView: UILabel, context: Context) {
        // Nothing yet.         
    }
}
```
👆 this will stretch the label all the way...i.e.

```
Text("hello")
Label("bye")
```

The UI would turn into: 

```
 ------
|hello |
 ------
 ------
|      |
|      |
| bye  |
|      |
|      |
 ------
```

hence we need to turn it into: 

```swift
struct MyLabel: View, UIViewRepresentable {
    var text: String 

    func makeUIView(context: Context) -> UILabel {
        let label = UILabel()
        label.text = text
        label.contentHuggingPriority set it to high for .vertical
        label.contentHuggingPriority set it to high for .horizontal
        return label
    }

    func updateUIView(_ uiView: UILabel, context: Context) {
        // Nothing yet.         
    }
}
```
👆 now the label will shrink nicely based on the proposed height...

Last but not least, you should also set change the `func updateUIView(_ uiView: UILabel, context: Context) {` to: 

```
func updateUIView(_ uiView: UILabel, context: Context) {
    uiView.text = text
}
```

## Why do we need a context?!

The relation ship is like this: 

```
instance1 of SomeView
instance2 of SomeView           -> Coordinator              -> UIView
instance3 of SomeView
```

We have instance 1-3 and more, because we make changes and that would make new instances for `SomeView`. But we want to have only one `Coordinator` for our one `UIView`. 
The `Context` is what holds onto that one `UIView` and that one `Coordinator` view. 
- 


```swift
@Environment(\.isEnabled) var isEnabled
```

it's just a given view can be made disabled. Simply by doing: 


```swift
DetailView(book: book)
.disabled(true)
```

👆 we're passing down an env variable that will set the `isEnabled` to `false`. 

3:16

Once you'd made a SwiftUI a `UIViewRepresentable`, then you should no longer set the `body` on the view. 
Otherwise you'd get an error saying: 

> 'UIViewRepresentable' requires the types 'some View' and 'Never' be equivalent

## How can I see what triggered the `body` update? 
`let _ = Self._printChanges`

The first view render will give you like: 
- `@self`
- `@identity`
- `any other state change`
- `any other env change`
- ...

but if the nex time you just toggled a Switch change, then you'll just get a print of: 
`<your-state-property> changed` or something. 

## Property Wrapper

It requires a wrapped value.
It doesn't require to have a projected value. If it doesn't have one then you can't the projected value. 
The Projected Value can be of any type. `Binding<Value>`, `Published<Value>.Publisher`, etc


### What's the difference between Property Wrapper vs Computed Property? 
- Computed Property isn't re-usable
- With Property wrapper, you can re-apply that property wrapper on a gazzilion other properties. 
- If you need to do the same, then you'd have to copy/paste that computation for every property and get the name right, etc. 
