---
title: "Everything You Need to Know About Keychain as an Ios Engineer"
date: 2022-05-06T13:21:51-04:00
draft: true
---

```
security find-identity -v -p codesigning
```

lists all your certs

```
security list-keychains -s `security list-keychains | xargs`  "/Users/mfaani908/Library/Keychains/personal.keychain-db"

```

helps add new keychain while maintaining the current list of keychains. 

Add a password to a keychain:

```
security add-generic-password -s 'gmail.com'  -a 'armin' -w 'password123'
```

get password from keychain:

```
security find-generic-password -w -s 'gmail.com' -a 'armin'
# password123
```

💡💡💡 in both the above commands, we didn't specify which keychain it should store into or retrieve from. The man page says: 

> If no keychain is specified, the password is added to the default keychain.

> If no keychains [you can use multiple keychains] are specified to search, the default search list is used.

### What's the difference between 'default search list' and 'search list'?

So you have a global 'default search list'. Think of that as a fallback. You can see its value by doing `security list-keychains`

So if you don't specify which keychains to look up from in your `find-generic-password`, then it will just look up from (default) list. If you do specify which keychains to look from, then it will only look from within those.

### So what happens if you have multiple items with the same name, in your search list?

The same problem that happens if you name all your children the same. If you have 3 kids named "Alex", then you never know who's going to respond first. 

But let's try that, see if 
## Jargon
- Keychain: Groups multiple items of your keychains together. You can one keychain only for Safari. Another just for Banking app. Or you could just store all items in one Keychain. Up to you.
- Keychain item: A password, cert, public key private key, credit card, note, etc. 
- Keychain search list: The certain Keychains that you want to look into. 

```
```
## Common problems
- Keychain is locked
- Another keychain item present in the search list with the same name, but the keychain is locked.
- Another keychain item present in the search list with the same name, but the keychain item is expired.
- 
Keychain won't get rid of expired certs. It will keep them. This can cause your problems. For more on that see [here](https://stackoverflow.com/questions/32821189/xcode-7-error-missing-ios-distribution-signing-identity-for/35401483#35401483). I suppose just like how expired certs don't get flused out and cause problems, expired profiles, don't get flushed out either. 
Similarly the password that logs you into your mac, is the password of your _login_ keychain. 
