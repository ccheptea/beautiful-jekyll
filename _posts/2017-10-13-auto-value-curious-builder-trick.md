---
layout: post
published: false
title: 'auto-value curious builder trick  '
subtitle: Curiously recurring template for AutoValue builders
---

As many people already know AutoValue is great. It is a helpful tool that takes care our model objects, makes them solid and immutable, has lots of extensions and generates \[a lot\] of code that otherwise would be our job to do. 

## Easy builder setup
Another good think about AutoValue is that it allows us to easily implement the builder pattern, reduced-to-an-interface easy.  Let's have a look at a real example. Suppose we want to log an event every time  a user a user signs in. Our event class will look like this:

```java
@AutoValue
abstract class SignInEvent{
    abstract String username();
    abstract String id();
    abstract String source();
    abstract String name();
    abstract Long timestamp();
    
    @AutoValue.Builder
	interface Builder{
       Builder username(String screenName);
                      
       Builder id(String id);
       Builder source(String source);
       Builder name(String name);
       Builder timestamp(Long timestamp);
       SignInEvent build();
    }
}
```

## Interface common properties

The above class is ok, but it has a flaw. What if we want to report another event: ScreenOpenEvent? Obviously, you get the point, we need an interface that will hold the common properties like so:

```java
// Event interface that holds common properties
interface Event {
    String id();
    String source();
    String name();
    Long timestamp();
}

@AutoValue
abstract class ScreenOpenEvent implements Event{
    abstract String screenName();
    
    @AutoValue.Builder
	interface Builder{
       Builder screenName(String screenName);
                      
       Builder id(String id);
       Builder source(String source);
       Builder name(String name);
       Builder timestamp(Long timestamp);
       SignInEvent build();
    }
}

@AutoValue
abstract class SignInEvent implements Event{
    abstract String username();
    
    @AutoValue.Builder
	interface Builder{
       Builder username(String screenName);
                      
       Builder id(String id);
       Builder source(String source);
       Builder name(String name);
       Builder timestamp(Long timestamp);
       SignInEvent build();
    }
}
```

This is much better. We still need to declare corresponding builder setter methods for each property present in the ```Event``` interface and AutoValue classes. Otherwise AutoValue will complaign (which is a positive thing). 