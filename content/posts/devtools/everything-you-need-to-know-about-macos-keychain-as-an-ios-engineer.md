---
title: "Everything You Need to Know About macOS Keychain as an iOS Engineer"
date: 2022-05-24T11:56:51-04:00
tags: [CI, jenkins, keychain, certificates, provisioning-profiles,security-command, macOS]
editPost:
    URL: 'https://github.com/prohoney/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
description: "Deep dive into how certificates are retrieved and common problem one can face for retrieving certificates for signing"
---
Please note this post will not go through what a profile or certificate is. Rather how it's all used together for signing and problems you could face during. 

In a nutshell, the way app signing works is that: 
All files are linked/compiled together. They create a binary/archive.  
That archive is signed using your provisioning profile and certificate.  
The signing is ultimately done using `codesign` command.  
If you're doing automatic signing, things are simple. Xcode will handle things for you and for the most part things should just work. 

However if you're signing things manually, then `codesign` will retrieve the certificate from your keychain. This is where things can get very complicated very quickly 🫠. The focus of this post is the certificate and its retrieval. 

But before we dive deep, into the internals it's really good if you go and poke around with your macOS's 'Keychain access' app. You can use [this](https://www.itproportal.com/guides/keychain-password-what-is-it-how-to-find-and-change-it/) tutorial. The things you need to try are: 
- Create a new (custom) keychain
- Create a keychain item 
- Open a keychain item 
- Delete a keychain
- Delete a keychain item 

👆 You might think that the above is a lot to learn, but it's a lot more simpler than you think. What's more interesting is that the OS has already done this for you without you noticing.

Anytime you login to an (email) account for the first time the OS prompts you with something like "Do you want to save (into the keychain) the username password you just entered for the gmail account you just logged in". The next time you go to gmail, assuming you've signed out, the OS pre fills the username/password for you as soon as you provide the keychain's password. The keychain's password is most likely is the password/fingerprint of your macOS.

### Jargon you should have learned by now
- Keychain: Groups multiple items/secrets together. You can have one keychain for Safari. Another just for a Banking app. Or you could just store all items in one Keychain. Up to you.
- Keychain item: A password, certificate, public key private key, credit card, note, etc. 
- Keychain search list: The certain Keychains that you want to look into. 

tldr think of Keychains as different vaults. Keychain items as unique valuable items in these vaults. And think of keychain search list as a way to group/order multiple vaults.

⚠️ Don't proceed, if you didn't poke around with the keychain access app. The rest of this post, won't make much sense if you didn't. ⚠️ 

-----

What you need to know is that the 'keychain access' app uses the `security` command under the hood. Keychain access lets you administer keychains, manipulate (public, private) keys, certificates, request certificates (from Apple), store credit card information, etc. 

## `security` command crash course with examples:

#### List all certificates (coupled with private keys) on your mac: 
```
$ security find-identity -v -p codesigning
1) 4E8D512C8480FAC679947D6E50190AE9BAB3E825 "3rd Party Mac Developer Application: Developer Name (DUCNFCN445)"
2) 8B0EBBAE7E7230BB6AF5D69CA09B769663BC844D "Mac Developer: Developer Name (DUCNFCN445)"
3) 4E8D512C8480AAC67995D69CA09B769663BC844D "iPhone Developer: App Developer (DUCNFCN445)"
4) 65E24CDAF5B3E1E1480818CA4656210871214337 "Developer ID Application: App Developer (DUCNFCN445)"
   4 valid identities found
```

If you see less than what you expect then it might be because you don't have the private key. A certificate without a private key is useless. Private key is the only thing that can prove it was signed by you. 

#### Add a password to a keychain:

```
security add-generic-password -s 'gmail.com'  -a 'armin' -w 'password123'
```

#### Get a password from keychain:

```
security find-generic-password -w -s 'gmail.com' -a 'armin'
# password123
```

💡💡💡 in both the above commands, we didn't specify which keychain it should store into or retrieve from. The man page says: 

> If no keychain is specified, the password is added to the default keychain.

> If no keychains [you can use multiple keychains] are specified to search, the default search list is used.

### What's the difference between 'default search list' and 'search list'?

So you have a global 'default search list'. Think of that as a fallback. You can see its value by doing `security list-keychains`

If you don't specify which keychains to look up from in your `find-generic-password`, then it will just look up from (default) list. If you do specify which keychains to look from, then it will only look from within those.

#### using 'default search list'
```
security find-generic-password -w -s 'gmail.com' -a 'armin'
```
 will only query from the (one and only) default keychain i.e. it will look in the 'default search list'

‼️ At least that's what's suppose to happen.

The above is based on the docs. But based on my findings the default keychain gets ignored when you look up things. 

#### Specifying a 'search list'
```
security find-generic-password -w -s 'gmail.com' -a 'armin' \
 "/Users/mfaani/Library/Keychains/business.keychain-db" \
 /Users/mfaani/Library/Keychains/personal.keychain-db"
```
 will query from "business.keychain-db" and "personal.keychain-db" i.e. it will look in the _specified_ search list


### So what really happens?
`security find-generic-password` looks for an item based on **the order of keychains**. 

Meaning if your `list-keychain` is in this order:

```
keychainBar  
keychainFoo (login)  
keychainBaz (default)
```

It will first try to find the item from KeychainBar.  
Then it will look into the second keychain, KeychainFoo  
If it can't find an item matching that name, then it will try keychainBaz. **The default keychain isn't used as a _default_.** 🤯🤯🤯


### Special Keychains
All macs come with a **login** keychain. All your passwords by default get stored into that.  
All macs also come with a **system** keychain. All your WiFi passwords get stored in that. 

The password of your macOS is the same password of your `login` keychain. 
Knowing that is important. Because you can safely assume that an agent that's logged in, will have access to the login keychain. But also if you ever run into an issue where you get a password prompt for your normal logins you'd know it's because the password between logging into your mac and the login keychain are off sync. For more on that see [here](https://support.apple.com/guide/keychain-access/mac-keychain-password-kyca1242/mac) and [here](https://apple.stackexchange.com/questions/153720/how-do-i-delete-or-fix-my-keychain-after-changing-my-password)



### How is a certificate identified/retrieved? 
- By its name
- By its SHA-1

Both the name and SHA-1 are auto generated. Like you can't name your certificate as "My Distribution cert: Meta Media". Upon generating Apple will generate the cert's name as such: "Apple Distribution: Meta Media" basically `<cert-type>:<apple-developer-team-name>`. 
So if you generate two distribution certificates then you will experience randomness if you used just the name. That's why it's best to use the SHA-1 of certificates

### Common problems
- **Locked keychain:** You attempt to access an item from a keychain that is locked. This will cause an OS system prompt which halts your CI process. 
- **Another locked keychain:** Your desired keychain was unlocked. Yet the provided identity (name or SHA-1) was present in _another locked_ keychain. The OS gave precedence to the locked keychain because it was the **first** keychain it saw in the _search list_. 
- **Different certificates with same name:** You're accessing a certificate with identical name, but the SHA-1 is different. 
- **Expired certificate with same name:** Another keychain item present in the search list with the same name, but the keychain item is expired.  
Keychain won't get rid of expired certificates. It will keep them. This can cause your problems. For more on that see [here](https://stackoverflow.com/questions/32821189/xcode-7-error-missing-ios-distribution-signing-identity-for/35401483#35401483). I suppose just like how expired certificates don't get flushed out and cause problems, expired profiles, don't get flushed out either. 

## Solutions and how to avoid Keychain Pollution?

#### Apple Developer portal todo:
- Have a single distribution certificate. Delete all others from your Apple developer account. You don't need to have two certificates for the same team. You only want two certificates, when you're near the expiration of your current certificate and you want to _transition_ to the other certificate. 
- Have a single distribution Provisioning profile. Delete all others from your Apple developer account. Often without knowing the CI uses an old profile that's using an old certificate, mac agents can be buggy with the way they cache things.

#### Proper specification at every point in Xcode and CI
- If you're using Xcode and some tool like fastlane, then make sure you: 
    - Use SHA-1 for codesigning identity inside Xcode's build settings
    - Use SHA-1 for codesigning identity inside your Fastfile
    - Use SHA-1 for codesigning identity inside your fastlane's plist
    - Use UUID for provisioning profile identifier inside Xcode
    - Use UUID for provisioning profile identifier inside your fastlane's plist
    Allowing these to go off sync can cause hard to track problems.

#### Keychain configurations
- Don't lock a keychain that you're about to use
- Don't add a short timeout on a keychain that contains your certificate. 
- To follow the [self-contained build script](https://mfaani.com/posts/what-is-a-self-contained-build-script/) logic, you should delete any similar named keychains. 

#### Keychain Triage
- Add logs in your CI to help triage things. Example: 
    - Log all the certificates on the machine: `security find-identity -v -p codesigning`
    - Log all the keychains in the search list: `security list-keychains`
    Basically you want to makes sure there isn't another keychain with the same SHA, but in a locked keychain that you didn't create and wasn't delete by others. 

#### Specify Keychain path 
- `codesign` also takes in `--keychain <keychain-path>` parameter. You can use that and specify the exact keychain to use. That would help disambiguate certificate look up. 
- If you're signing with `xcodebuild` then you can do:
    - `xcodebuild "OTHER_CODE_SIGN_FLAGS=--keychain '$PATH_TO_KEYCHAIN'"`
    - or within a `Fastfile` you can pass the following `"OTHER_CODE_SIGN_FLAGS='--keychain ~/Library/Keychains/AppPrivate.keychain-db'"` as an `xcargs` parameter of `build_ios_app` or `gym`

### References: 

- [Ask the Experts A Deep Dive into Keychain and Spotlight Artifacts](https://www.youtube.com/watch?v=F7wVrbeZ2B4)
- [Fastlane docs - Common code signing issues](https://docs.fastlane.tools/codesigning/common-issues/)
- [objc - Inside Code Signing](https://www.objc.io/issues/17-security/inside-code-signing/)
- [Code signing changes from Xcode 7 to Xcode 8](https://pewpewthespells.com/blog/migrating_code_signing.html)
- [A good Q&A on certain restrictions Apple has put in place for how keychain works](https://www.patreon.com/posts/14556409)