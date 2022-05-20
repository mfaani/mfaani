---
title: "Everything You Need to Know About Keychain as an ios Engineer"
date: 2022-05-06T13:21:51-04:00
draft: true
---
Please note through out this post I'll use the term cert/certificate and profile/provisioning profile interchangeably. This post will not go through what a profile or cert is. Rather how it's all used together for signing and problems you could face during. 

The way app app signing works is that: 
All files are linked/compiled together. They create a binary/archive.  
That archive is signed using your provisioning profile and certificate.  
The signing is ultimately done using `codesign` command.  
If you're doing automatic signing, things are simple. Xcode will handle things for you and for the most part things should just work. 

However if you're signing things manually, then `codesign` will only retrieve the certificate from your keychain. This is where things can get very complicated very quickly 🫠.

But before we dive deep, let's do a crash course on `security` command. 

Essentially it lets you administer keychains, manipulate (public, private) keys, certificates, request certificates (from Apple), store credit card information, etc. 

## Some examples of `security` command

### lists all your certs: 
```
security find-identity -v -p codesigning
```

### Add a password to a keychain:

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

Having that said, I was able to figure out the order in which keychain retrieves items. 

Simply put it's the lookup happens `list-keychain` trumps even the _default-keychain_.

Meaning if your `list-keychain` is in this order:

```
KeychainB  
KeychainA (login)  
keychain C (default)
```

it will first try to find the item from KeychainB. 


## Jargon
- Keychain: Groups multiple items of your keychains together. You can one keychain only for Safari. Another just for Banking app. Or you could just store all items in one Keychain. Up to you.
- Keychain item: A password, cert, public key private key, credit card, note, etc. 
- Keychain search list: The certain Keychains that you want to look into. 


## Login Keychain is special
The password of your macOS is the same password of your `login` keychain. It may go off sync: https://support.apple.com/guide/keychain-access/mac-keychain-password-kyca1242/mac 
https://apple.stackexchange.com/questions/153720/how-do-i-delete-or-fix-my-keychain-after-changing-my-password

## Keychain access restraints
https://www.patreon.com/posts/14556409

## Common problems
- Keychain is locked
- Another keychain item present in the search list with the same name, but the keychain is locked.
- Another keychain item present in the search list with the same name, but the keychain item is expired.
- 
Keychain won't get rid of expired certs. It will keep them. This can cause your problems. For more on that see [here](https://stackoverflow.com/questions/32821189/xcode-7-error-missing-ios-distribution-signing-identity-for/35401483#35401483). I suppose just like how expired certs don't get flushed out and cause problems, expired profiles, don't get flushed out either. 
Similarly the password that logs you into your mac, is the password of your _login_ keychain. 


https://www.youtube.com/watch?v=F7wVrbeZ2B4

https://docs.fastlane.tools/codesigning/common-issues/

https://gist.github.com/digiter/9c3c64dbdb73c27af730c6e1b04828eb

https://pewpewthespells.com/blog/migrating_code_signing.html