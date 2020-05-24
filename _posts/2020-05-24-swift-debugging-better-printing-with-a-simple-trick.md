---
layout: post
published: false
title: "Swift Debugging: better printing with a simple trick \U0001F98B\U0001F98E⚠️❌"
---

Coming from Android development to iOS/Swift, one might expect debugging to be somewhat similar. It is to some extent. One thing that I miss while doing iOS development is the logging capabilities Android Studio offers, which is available out of the box. 

I use `print()` a lot while doing development. I like that it is so basic and simple to use, but often times I find myself doing extra work to make the logs easier to follow. Things like timestamps and types (debug/error/warning/info) are things that make it a lot easier to filter and analyze. Same goes metadata such as class/filename, function and line where the log was created.

This made do a little research that resulted in a few lines of code that define the following methods:


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