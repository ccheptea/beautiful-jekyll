---
layout: post
published: true
title: Handling REST errors with RxSwift and RxAlamofire
date: '2019-03-25'
subtitle: Use enums and extension functions to simplify consuming REST APIs.
---

If you're developing an iOS app that consumes a REST API, chances are you're using RxAlamofire. If you don't, then it is worth exploring what it has to offer. For me personally, transitioning to iOS/Swift from an Android/Kotlin world was fairly smooth. The two languages share a lot of similarities. And if you're willing to look, there are plenty of (almost) equivalent libraries for both platforms. 

The first thing I integrated into my iOS app was RxSwift, followed by Alamofire (a twin for Retrofit) and RxAlamofire. RxAlamofire is a wrapper on top of Alamofire that offers all the good stuff Rx has in its trunk.

Making a network call with RxAlamofire is as simple as:

```swift
_ = session.rx
    .request(.get, url)
    .responseData()
    .subscribe(onNext: { response in
    	// handle response
    }, onError: { error in
    	// handle error
    })
```


## Mistakes

A common mistake people usually make when consuming a REST API is relying solely on the happy flows. After all, it is easier to consider the API infallible and blame the backend if anything goes wrong. That is something to expect as everyone loves rewards and dreads failures. Hence, sometimes errors are ignored.

Another mistake is treating all errors in the same way. With Rx, it is quite easy to add a handler for errors, but even then it is tempting to tell the user "Something went wrong. Please try again later. Thanks!". However, errors are not the same and in order to avoid confusing users, we need to provide appropriate feedback.


## Types of Errors

Generally, when making an HTTP call with RxAlamofire there are three categories of errors:

1. Errors raised on the client (no connectivity, errors while processing the response)
2. Expected errors originating on the API (unauthorized user, invalid credentials, resources not found)
3. Unexpected errors originating on the API (internal server error)

Usually, errors in the first category come with an explicit message that we can present to the user. For instance, if there is no internet connection we can display the error message in the `onError` callback.

The second and third categories are a bit trickier. That is because when making a call to the endpoint and we do get a response, Alamofire considers it successful, regardless of its status code. 

So, how do we model our data in order to easily handle responses that could represent either a success or an error?

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

The above code can be read as: __An API call result can succeed with a Value object or fail with an Error object__. 

Now let's write some models. Consider an authentication endpoint that expects an email and a password, and responds with a JSON containing a user id and a token if the request is successful or a JSON containing an error message if it is not successful.

Successful response JSON example:
```json
{
    "user_id": "123",
    "token": "c7d83mdla_3ms"
}
```

Failure response JSON example:
```json
{
    "error_message": "User doesn't exist."
}
```

In Swift this can be translated to the following:

```swift
struct LoginResponse: Codable{
    let user_id: String,
    let token: String
}

struct ApiErrorMessage: Codable{
    error_message: String
}
```

For the sake of simplicity, let's consider that the scheme of the error message is always the same. 

## Handling Responses

In order to make things work, we have to teach RxAlamofire to map the actual response to the correct type: `LoginResponse` or `ApiErrorMessage`. To do that let's write an extension function for `Observable` which we will use to instruct RxAlamofire about our expected response.

```swift
extension Observable where Element == (HTTPURLResponse, Data){
    fileprivate func expectingObject<T : Codable>(ofType type: T.Type) -> Observable<ApiResult<T, ApiErrorMessage>>{
        return self.map{ (httpURLResponse, data) -> ApiResult<T, ApiErrorMessage> in
            switch httpURLResponse.statusCode{
            case 200 ... 299:
                // is status code is successful we can safely decode to our expected type T
                let object = try JSONDecoder().decode(type, from: data)
                return .success(object)
            default:
                // otherwise try
                let apiErrorMessage: ApiErrorMessage
                do{
                    // to decode an expected error
                    apiErrorMessage = try JSONDecoder().decode(ApiErrorMessage.self, from: data)
                } catch _ {
                    // or not. (this occurs if the API failed or doesn't return a handled exception)
                    apiErrorMessage = ApiErrorMessage(errorMsg: "Server Error.")
                }
                return .failure(apiErrorMessage)
            }
        }
    }
}
```

Phew! That is quite some information to grasp, but if you look closer it is not that complicated. What the function does, is to tell the observable how to convert the data based on the response's status code and what to send further down the pipe. You can easily add more cases for other status codes, depending on your needs.

Now let's see how our login call will look like.

```swift
_ = manager.rx
    .request(.post, "https://my-api.com/login",
        parameters: ["email": john@doe.com, "password": "onlyjohnknowsme"])
    .responseData()
    .expectingObject(ofType: LoginResponse.self) // <-- specify what object is expected
    .subscribe(onNext: { apiResult in
        switch apiResult{
        case let .success(loginResponse):
            // handling the successful response
            saveUserId(loginResponse.user_id)
            saveToken(loginResponse.token)
        case let .failure(apiErrorMessage):
            // handling the erroneous response
            showError(apiErrorMessage.error_msg)
        }
    },onError:{ err in
        // handle client originating error
    })
```

## Conclusion

Although being new to iOS development, Swift offers handy tools to help write good code. In this particular case, we eliminated the response type ambiguity when consuming a REST API which implicitly helps us improve the end user experience. We modelled our responses using `enums` and added a superpower to the Observable type by writing an extension function.
