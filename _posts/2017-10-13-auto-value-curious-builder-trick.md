---
layout: post
published: false
title: 'auto-value curious builder trick  '
subtitle: Curiously recurring template for AutoValue builders
---

As many people already know AutoValue is great. It is a helpful tool that takes care our model objects, makes them solid and immutable, has lots of extensions and generates \[a lot\] of code that otherwise would be our job to do. 

Another good think about AutoValue is that it allows us to easily implement the builder pattern. Let's have a look at a real example. Suppose we want to log events every time  a user a user signs in and when a new screen is opened. Both events have several common properties and some custom properties as well. The code below represents the models for these two events.

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