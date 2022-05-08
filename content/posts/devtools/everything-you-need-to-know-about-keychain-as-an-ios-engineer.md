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
