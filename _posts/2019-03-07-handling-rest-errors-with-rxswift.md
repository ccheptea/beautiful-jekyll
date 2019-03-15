---
layout: post
published: false
title: Handling REST errors with RxSwift
---

## Consuming a REST API 

If you're developing an iOS app that consumes a REST API, chances are you're using RxAlamofire. If you don't, then it is worth exploring what it has to offer. For me personally, transitioning to iOS/Swift from an Android/Kotlin world was fairly smooth, since the two languages share a great deal of similarities. And if you're willing to look, there are plenty of (almost) equivalent libraries for both platforms. 

The first thing I integrated in my iOS app was RxSwift, followed by Alamofire (a twin for Retrofit) and RxAlamofire. RxAlamofire is a wrapper on top of Alamofire that offers all the good stuff Rx has in its trunk.

Making a network call with RxAlamofire is as simple as:

```swift
_ = manager.rx
	.request(.get, url)
    .responseData()
    .asSingle()
    .subscribe(onSuccess: { response in
    	// handle response
    }, onError: { error in
    	// handle error
    })
```


## Mistakes

A common mistake people usually make when consuming a REST API is relying solely on the happy flows. After all, it is easier to consider the API infallible and blame the backend guys if anything goes wrong. That is something to expect as usually one wants to see quick results and get a dosage of serotonin. Everyone loves rewards and dreads failures. I'm no exception to that rule. Hence, sometimes errors are ignored. 
Another mistake is treating all errors the same way. With Rx it is quite easy to add a handler for errors, but even then it is tempting to tell the user "Something went wrong. Please try again later. Thanks!". 


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