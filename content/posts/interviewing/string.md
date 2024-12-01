---
title: "Swift Strings for iOS interviewing"
date: 2022-06-12T14:49:48-04:00
category: [interviewing]
tags: [swift, strings, unicode, character]
description: "Some discussion on Unicode and clustering and why converting a string to an array of characters makes things easier"
---

This is bit of fast paced intro into Swift Strings. 

## ASCII, Unicode and the challenges they introduce. 

### ASCII
Ages ago, only characters that existed were just `a` to `z`, `A` to `Z`, and bunch of other English characters. 
This was problematic. You were limited to only 7 bits i.e. only `2 ^ 7 - 1 = 127` characters. 
Also non-English characters where not part of ASCII. Literally the name says 'American Standard Code for Information Interchange'. So characters like `å` or `ä` were not part of it. Let alone `ب` or `ج` or characters of other languages. 

#### Character mapping in ascii

```swift
let uppdercaseA: Character = "A"
print(String(describing: uppdercaseA.asciiValue)) // 65

let lowercaseA: Character = "a"
print(String(describing: lowercaseA.asciiValue)) // 97

let variantA: Character = "á"
print(String(describing: lowercaseA.asciiValue)) // nil
```

### Unicode
Then came Unicode. It was roomy. Currently Unicode has `144,697` characters. 
- Unlike ASCII where it only supported English, Unicode supports characters in most languages.
- It has the same concept of mapping in ASCI. 
- Anything that is in asci will have the same value it did in Unicode. This helps backwards compatibility.
- Has combining logic. Examples: 
    - `"a"` + `" ́"` together will create `"á"`.  You'd have to write it as such: `"a\u{301}"`. `\u{<code point>}`
    - `🇺` + `🇸` together will create 🇺🇸 . For more on that see https://en.wikipedia.org/wiki/Regional_indicator_symbol
- Similar to how Hex is prefixed with `0x`, Unicode is prefixed with `U+`
    - U+1F600 (😀 emoji)
    - U+0041 ("A")
- Unicode has [blocks](https://en.wikipedia.org/wiki/Unicode_block#List_of_blocks) for each language. For more on that see my other post on [Why can't you loop over ranges of characters - benefits of keeping all characters of a language grouped together](http://localhost:1313/posts/swift/why-cant-you-loop-over-ranges-of-characters-in-swift/#are-there-benefits-to-keeping-all-characters-of-a-language-grouped-together)

Unicode is a giant table of code-points (Swift calls scalars) matching to some full characters, some combining accents, and some invisibles.


### (Swift) Character
It's a heavily overloaded term. Anything perceivable by us as humans as a single character. `a`, `â`, `õ`, `!`, `~`,` `, `1` etc. 
A character is more or less an array (one or more) code points without a (grapheme) break. Examples: 

```swift
let x = "a"
let y = "á"
let z = "a\u{301}"

func visualize(_ string: String) {
    print("string:",string)
    print("used code points:")
    for s in string.unicodeScalars {
        print(s,":",s.value)
    }
    print("----")
}

visualize(x)
visualize(y)
visualize(z)
```

Output:

```swift
string: a
used code points:
a : 97 // See https://unicode.scarfboy.com/?s=U%2b0061
----
string: á
used code points:
á : 225 // See https://unicode.scarfboy.com/?s=U%2b00E1
----
string: á
used code points:
a : 97 // See https://unicode.scarfboy.com/?s=U%2b0061
́ : 769 // See https://unicode.scarfboy.com/?s=U%2b0301
----
```

What's interesting is that `a\u{301}` produces `á`. Unicode has logic to combine code points. Much like how Math knows how to combine things. i.e. 

```swift
let y = "á"
let z = "a\u{301}"

print(y == z) // true
```

Think of how `5 + 3` is equal to `7 + 1` yet if you compared the two literally then they're obviously not the same. 

> `Character` comparisons occur on the final rendered Character. Not the the literal form of it. 

### 👆 is one of the main reasons why Swift Strings are complicated. 

Like if you're reading 

`a` then its palindrome is just `a`.  
However if you're reading `á` then it's reverse could be either: 
- `á`  (the reverse of y)
- ` ́a` (the reverse of z)

i.e. one could argue that the palindrome of `á` is ` ́a` i.e. two code points can end up being palindrome with one code point. 

Depending on how the string/character is constructed the character count could be different —  i.e. `a` is 1 character, but `á` can be either just `á` or `a ́`. 

Also 


### Other notes from https://unicode.org/glossary
- A grapheme is a logical entity, not visual: it can be composed of one or multiple code points. 

- A "glyph", also in the glossary (https://unicode.org/glossary/#glyph) which is typically used to mean "the drawn representation of graphemes".

- Italic and bold don't create a different code point. If they did, then Unicode would have exploded. Basically:
  - `grapheme + (italic) font -> (italic) glyph`
  - `grapheme + (bold) font -> (bold) glyph`

## What should you do in interviews? 

If all you want is **looping** then do: 

Just do: 
```swift
let str = "hello world"

for s in str {
    print(type(of:s))
} 
```

If you need **indexing** then without giving away too much time for explanation just convert the string to an array of characters. 99.99% of questions you get asked aren't considerate of previous Unicode complexities.
```
let str = "hello world"
let characters = Array(str)

print([characters[2]]) // "l"
 
```

However if you're confident and you're not losing time or you think it works to your advantage then discuss the complexities. It could help you stand out vs. the rest of the pack. 

Converting a string to an array has a time complexity of `O(n)`. Since most algorithmic problems can't be solved better than O(n) then it's totally fine to do so. 

### What if I don't want/can't use an array of Characters? 
Try mastering the [String - Manipulating indices](https://developer.apple.com/documentation/swift/string) section. 

A helper method I use is this:

```swift
extension String { 
    func at(_ i: Int) -> Character {
        return self[self.index(self.startIndex, offsetBy: i)]
    }
    
    func safeAt(_ i: Int) -> Character? {
        guard i < count  else { return nil }
        return at(i)
    }
}
```

Also it's good to understand that [`startIndex`](https://developer.apple.com/documentation/swift/string/startindex) is the position of the first character in an _nonempty_ string. 

The [endIndex]() is: A string’s "past the end" position -- that is, the position one greater than the last valid subscript argument.

With an empty string, accessing the `startIndex` and `endIndex` will both cause crashes.
In an empty string, `startIndex` is equal to `endIndex`. 

#### Why is `endIndex` "past the end" position?!

If endIndex were the last valid subscript, then a question would arise as: in an empty collection what should the value of `endIndex` be? You can't change the logic for calculating the endIndex — only for empty arrays. You need consistency.

- [first](https://developer.apple.com/documentation/swift/array/first) and [last](https://developer.apple.com/documentation/swift/array/last) return **optional** 
values because the thing might be empty, so there is no "first" nor "last" element or index

- On the other hand, `startIndex` is where the collection begins, and whether it’s empty or non-empty, an array **always** begins at 0. 
Because of this, accessing an array using `startIndex` or `endIndex` can cause out of bounds crashes. Example: 

```swift
extension String { 
    func at(_ i: Int) -> Character {
        return self[self.index(self.startIndex, offsetBy: i)] // ❌ CRASH: String index is out of bounds ❌
    }
}

let ss: String = ""
ss.at(0)
```

This is also partly for ease of comparisons/calculations:

- An array's count is `endIndex - startIndex`
- `isEmpty` is `startIndex == endIndex`

If `endIndex` was the last index, you'd need to do extra math in there for those calculations.

The [docs](https://developer.apple.com/documentation/swift/string) have a nice example of putting the above together to: 

```swift
let name = "Marie Curie"
let firstSpace = name.firstIndex(of: " ") ?? name.endIndex
let firstName = name[..<firstSpace]
print(firstName)
// Prints "Marie"
```

Try not to memorize the above. If you understand the 'why' then you'll memorize it naturally. 

Also see this discussion in the [dev forums](https://forums.swift.org/t/collections-past-the-end-endindex/3249/2) and [the original post by Dijkstra](https://www.cs.utexas.edu/users/EWD/transcriptions/EWD08xx/EWD831.html) himself

### if empty check
To check whether a string is empty, use its `isEmpty` property instead of comparing the length of one of the views to 0. Unlike with `isEmpty`, calculating a view’s `count` property requires iterating through the elements of the string.

## Summary

- `a` has a code point of 97. It's a grapheme by itself.  
- ` ́` has a code point of 769. It's a grapheme by itself.  
- Together they form a new grapheme cluster: `á`.  
- `á`, _`á`_, **`á`** are all the same grapheme, but because of the font they're different glyphs.  
- For interviewing most people that are comfortable with Swift, find it easier to convert the string to an array of Characters. Because the focus of the interview isn't on your String skills it's about your interview, algo and DS abilities. Or at least that's what it should be.


## References
- Also recommend seeing my other post on [Why can't you loop over a Range of Swift Characters](http://localhost:1313/posts/swift/why-cant-you-loop-over-ranges-of-characters-in-swift/)
- [Stackoverflow - What's the difference between a character, a code point, a glyph and a grapheme?](https://stackoverflow.com/questions/27331819/whats-the-difference-between-a-character-a-code-point-a-glyph-and-a-grapheme)
- [What's the difference between ASCII and Unicode?](https://stackoverflow.com/questions/19212306/whats-the-difference-between-ascii-and-unicode)
- [Quora - What's the difference between a character, a glyph, and a grapheme?](https://www.quora.com/Whats-the-difference-between-a-character-a-glyph-and-a-grapheme)
- [Unicode Glossary](https://unicode.org/glossary)