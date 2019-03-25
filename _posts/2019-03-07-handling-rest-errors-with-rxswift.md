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

A common mistake people usually make when consuming a REST API is relying solely on the happy flows. After all, it is easier to consider the API infallible and blame the backend guys if anything goes wrong. That is something to expect as everyone loves rewards and dreads failures. I'm no exception to that rule. Hence, sometimes errors are ignored.

Another mistake is treating all errors the same way. With Rx it is quite easy to add a handler for errors, but even then it is tempting to tell the user "Something went wrong. Please try again later. Thanks!". But errors are not the same and in order to avoid confusing users we need to provide appropriate feedback.


## Types of Errors

Generally, when making an http call with RxAlamofire there are three categories of errors:

1. Errors raised on the client (e.g.: no connectivity, errors while processing the response etc.)
2. Expected errors originating on the API (e.g.: unauthorized user, invalid credentials, resources not found etc.)
3. Unexpected errors originating on the API (e.g.: internal server error)

Errors in the first category usually come with an explicit message that we can present to the user. For instance, if there is no internet connection we can display the error message in the `onError` callback from the above snippet.

The second and third categories are a bit trickier. That is because when making a call to the endpoint and we do get a response, Alamofire (and probably all HTTP clients) considers it successful, regardless of its status code. 

So how do we model our data in order to easily handle responses that could represent either a success or an error?

## Modeling Responses

One thing I like about Swift is that enums can be instantiated with an associated value. This increases type-safety and allows us to better model our business logic. To make things simpler, here is how we can define our response:

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

The above code can be read as: __An API result can succeed with a Value object or fail with an Error object__. 




