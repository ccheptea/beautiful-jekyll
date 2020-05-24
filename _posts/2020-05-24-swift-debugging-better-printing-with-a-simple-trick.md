---
layout: post
published: false
title: "Swift Debugging: better printing with a simple trick \U0001F98B\U0001F98E⚠️❌"
---

Coming from Android Development to iOS/Swift, one might expect debugging to be somewhat similar. It is to some extent. One thing that I miss while doing iOS Development is the logging capabilities Android Studio offers, which are available out of the box.

I use print() a lot while during development. I like that it is so basic and simple to use, but oftentimes I find myself doing extra work to make the logs easier to follow and dissect. Things like timestamps and types (debug/error/warning/info) are things that make it a lot easier to filter and analyze the logs. Same goes metadata such as class/filename, function and line where the logs were created. 

This made me do a little research that resulted in a few lines of code that define the following methods:


```swift
info("Some butterflies are blue.")
warning("Careful, something's cooking!")
debug("Here's a list of primes:", 2, 3, 5, 7)
error("Ooops, you've got errors!")
```

which print logs like this:


```
15:38:21.816 🦋 TabViewScreen.init():46 Some butterflies are blue.
15:38:21.816 ⚠️ TabViewScreen.init():47 Careful, something's cooking!
15:38:21.816 🦎 TabViewScreen.init():48 Here's a list of primes: 2 3 5 7
15:38:21.816 ❌ TabViewScreen.init():49 Ooops, you've got errors!
```

The idea was to preserve the `print` signature, since that's what I was used to, while using more descriptive names as well.

Here's a gist with the code I'm using, but feel free to customizing to your own needs. 

{% gist 324e40dc905c961d87a62f65f7ba0462 %}

Note: unfortunately XCode doesn't allow text coloring in the console. (Or I didn't find how to do it yet)
