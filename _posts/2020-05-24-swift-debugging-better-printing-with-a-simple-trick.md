---
layout: post
published: true
title: "Swift Debugging: better printing with a simple trick \U0001F98B\U0001F98E⚠️❌"
subtitle: 'Add timestamps, source and emoji''s to Swift logs.'
date: '2020-05-24'
tags:
  - swift
  - iOS
  - xcode
  - debugging
  - logs
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

The idea was to preserve the `print` signature, since that's what I was used to, while using more descriptive names.

Here's a gist with the code I'm using, but feel free to customizing to your own needs.

[_logging.swift](https://gist.github.com/ccheptea/324e40dc905c961d87a62f65f7ba0462)

```swift

import Foundation

fileprivate let infoMarker = "🦋"
fileprivate let debugMarker = "🦎"
fileprivate let warningMarker = "⚠️"
fileprivate let errorMarker = "❌"

func info(_ items: Any..., separator: String = " ", terminator: String = "\n", file: String = #file, line: Int = #line, function: String = #function) {
    log(items, separator: separator, terminator: terminator, marker: infoMarker, file: file, function: function, line: line)
}

func debug(_ items: Any..., separator: String = " ", terminator: String = "\n", file: String = #file, line: Int = #line, function: String = #function) {
    log(items, separator: separator, terminator: terminator, marker: debugMarker, file: file, function: function, line: line)
}

func warning(_ items: Any..., separator: String = " ", terminator: String = "\n", file: String = #file, line: Int = #line, function: String = #function) {
    log(items, separator: separator, terminator: terminator, marker: warningMarker, file: file, function: function, line: line)
}

func error(_ items: Any..., separator: String = " ", terminator: String = "\n", file: String = #file, line: Int = #line, function: String = #function) {
    log(items, separator: separator, terminator: terminator, marker: errorMarker, file: file, function: function, line: line)
}

fileprivate var formatter: DateFormatter = {
    let _formatter = DateFormatter()
    _formatter.dateFormat = "H:m:ss.SSS"
    return _formatter
}()

fileprivate func log(_ items: [Any], separator: String = " ", terminator: String = "\n", marker: String, file: String, function: String, line: Int) {
    let lastSlashIndex = (file.lastIndex(of: "/") ?? String.Index(utf16Offset: 0, in: file))
    let nextIndex = file.index(after: lastSlashIndex)
    let filename = file.suffix(from: nextIndex).replacingOccurrences(of: ".swift", with: "")
    
    let dateString = formatter.string(from: NSDate.now)
    
    let prefix = "\(dateString) \(marker) \(filename).\(function):\(line)"
    
    let message = items.map {"\($0)"}.joined(separator: separator)
    print("\(prefix) \(message)", terminator: terminator)
}


```

**Note\***: unfortunately XCode doesn't allow text coloring in the console. (Or I didn't find how to do it yet)

**Note\*\***: The above code is a simple/lightweight and straightforword solution that doesn't require including any external package. If you want something more sophisticated you can check some other tools like this one: https://github.com/SwiftyBeaver/SwiftyBeaver
