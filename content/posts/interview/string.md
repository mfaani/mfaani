---
title: "String"
date: 2022-05-01T14:49:48-04:00
draft: true
---

This is bit of fast paced intro into Swift Strings. 

### ASCII
Ages ago, only characters that existed were just `a` to `z`, `A` to `Z`, and bunch of other English characters. 
This was problematic. You had to store it all in 7 bits. `2 ^ 7 = 128` 
Non-English characters where not part of ASCII. Literally the name says 'American Standard Code for Information Interchange'. So characters like `å` or `ä` were not part of it. Let alone `ب` or `ج` or characters of other languages. 

```swift
let uppdercaseA: Character = "A"
print(String(describing: uppdercaseA.asciiValue)) // 65

let lowercaseA: Character = "a"
print(String(describing: lowercaseA.asciiValue)) // 97

let variantA: Character = "á"
print(String(describing: lowercaseA.asciiValue)) // nil
```

### Character
It's a heavily overloaded term. Anything perceivable by us as humans as a single character. `a`, `â`, `õ`, `!`, `~`,` `, `1` etc. 

The following are not a character: `10`, ‍`◌́`‌(see [here](https://en.wikipedia.org/wiki/Acute_accent)) are not characters. 
- `10` is a collection of characters
- `◌́`‌ is a modifier. Example `a` and `◌́`‌ together create `á`. `á` was not possible in ASCII.  

### Unicode
Then came Unicode. It was roomy. Currently Unicode has `144,697` characters. 
- It has the same concept of mapping in ASCI. 
- Anything is asci will have the same value it did in Unicode. 

```swift
let ss: String = "A"
print(character.asciiValue) // Optional(65)
print(ss.utf8) // 65
print(ss.utf16) // 65
```

### Unicode 

## References
https://stackoverflow.com/questions/27331819/whats-the-difference-between-a-character-a-code-point-a-glyph-and-a-grapheme
https://stackoverflow.com/questions/19212306/whats-the-difference-between-ascii-and-unicode
https://www.quora.com/Whats-the-difference-between-a-character-a-glyph-and-a-grapheme
https://stackoverflow.com/questions/27331819/whats-the-difference-between-a-character-a-code-point-a-glyph-and-a-grapheme