---
layout: post
published: false
title: Handling REST errors with RxSwift
---

If you're developing an iOS app that consumes a REST api, chances are you are using RxAlamofire. If you don't, then it is worth exploring what it has to offer. For me personally, transitioning to iOS/Swift from an Android/Kotlin world was fairly smooth, since the two languages share a great deal of similarities and if you're willing to look, there are plenty of (almost) equivalent libraries for both platforms. 

The first thing I integrated in my iOS app was RxSwift, followed by Alamofire (a twin for Retrofit) and RxAlamofire. RxAlamofire is a wrapper on top of Alamofire that offers all the good stuff Rx has in its trunk.

Making a network call with RxAlamofire is as simple as:

```swift
_ = session.rx
    .data(.get, stringURL)
    .observeOn(MainScheduler.instance)
    .subscribe { print($0) }
```
  

```swift

enum ApiResult<Value, Error>{
    case success(Value)
    case failure(Error)
    
    init(value: Value){
        self = .success(value)
    }
    
    init(error: Error){
        self = .failure(error)
    }
}
```