---
title: "Unicode Security"
date: 2022-06-17T00:06:40-04:00
category: ['web']
tags: ['web', 'unicode', 'punycode', 'security', 'browsers']
description: "How Unicode can cause chaos"
editPost:
    URL: 'https://github.com/prohoney/mfaani/tree/main/content'
    Text: Suggest Changes
    appendFilePath: true
---

This post is the result of some post-discussion on my [Swift Strings for iOS interviewing](https://mfaani.com/posts/interviewing/string/) post. 

Given that characters look like one another or that are invisible codepoints I thought there might be some room for abuse. Luckily Unicode has great documentation security. At the high level there are two types of security issues:

### Visual Security Issues

> Suppose that the user gets an email notification about an apparent problem in their Citibank account. Security-savvy users realize that it might be a spoof; the HTML email might be presenting the URL http://citibank.com/... visually, but might be hiding the real URL. They realize that even what shows up in the status bar might be a lie, because clever Javascript or ActiveX can work around that. (And users are likely to have these turned on, unless they know to turn them off.) They click on the link, and carefully examine the browser’s address box to make sure that it is actually going to http://citibank.com/.... They see that it is, and use their password. However, what they saw was wrong—it is actually going to a spoof site with a fake “citibank.com”, using the Cyrillic letter that looks precisely like a ‘c’. They use the site without suspecting, and the password ends up compromised.

### Non-visual Security Issues

> For example, suppose that strings containing the letters “delete” are sensitive internally, and that therefore a gatekeeper checks for them. If some process casefolds “DELETE” after the gatekeeper has checked, then the sensitive string can sneak through. While many programmers are aware of this, they may not be aware that the same thing can happen with other transformations, such as an NFKC transformation of “Ⓓⓔⓛⓔⓣⓔ” into “delete”.

Another problem is similar looking characters. They are known as [confusables](https://util.unicode.org/UnicodeJsps/confusables.jsp).
![Unicode Confusables](images/confusables.png "Similar looking characters")

Scroll all the way to the right to see all the confusables. One of main places where having knowledge about confusables is domain names.  
What happens if instead of typing `www.google.com`, someone is given a fake URL of `www.gòogle.com`. 
Or what happens if instead of `https://www.bankofamerica.com` someone is given a fake URL of `https://www.bânkofamerica.com`. 

Well let's try them: 
   
```
gòogle.com --> xn-gogle-uta.com
```

![Fake Google](images/fake-google.png "Safari Can't Find the Server")


```
bankofamerica.com --> xn-bnkofamerica-pbb.com
```

![Fake BofA](images/fake-bankofamerica.png "Safari Can't Find the Server")

### That's weird. Why is the URL changing?

Anytime a confusable is part of the domain name, then the browser will alter the domain name to something else. The end result is a confusing and perhaps ugly domain. Browsers purposely make the domain name ugly, so users are alerted that the URL they used is phony. Upon seeing that the user is more likely to double check things and perhaps not enter credentials for that URL.

Using more technical terms:

> [Browsers](https://en.wikipedia.org/wiki/Web_browser) will typically turn [confusables](https://util.unicode.org/UnicodeJsps/confusables.jsp) into [punycode](https://en.wikipedia.org/wiki/Punycode)

> Punycode is a representation of Unicode with the limited ASCII character subset used for Internet hostnames. Using Punycode, host names containing Unicode characters are transcoded to a subset of ASCII consisting of letters, digits, and hyphens, which is called the letter–digit–hyphen (LDH) subset. For example, `München` (German name for Munich) is encoded as `Mnchen-3ya`.


### Acknowledgements 

Shout out to [Mark Rowe](https://twitter.com/bdash) who answered all my questions so I can put together this post. 
