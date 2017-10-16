---
layout: post
published: true
title: 'auto-value curious builder trick  '
subtitle: Curiously recurring template for AutoValue builders
date: '2017-10-15'
---

As many people already know, [AutoValue](https://github.com/google/auto/blob/master/value/userguide/index.md) is great. It is a helpful tool that takes care of our model objects, makes them solid and immutable, has lots of extensions and generates \[a lot of\] code that otherwise would be our job to write. 

## Basic Builders 
Another good thing about [AutoValue](https://github.com/google/auto/blob/master/value/userguide/index.md) is that it allows us to easily implement the builder pattern -  reduced-to-an-interface easy.  Let's see an example. Suppose we want to log an event every time a user signs in. Our event class will look like this:

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
       Builder username(String username);
                      
       Builder id(String id);
       Builder source(String source);
       Builder name(String name);
       Builder timestamp(Long timestamp);
       SignInEvent build();
    }
}
```

## Interface common properties

The above class is ok, but it has a flaw. What if we want to report another event: ScreenOpenEvent? Obviously, we need an interface that will hold the common properties.

```java
// Event interface that holds common properties
interface Event {
    String id();
    String source();
    String name();
    Long timestamp();
}

@AutoValue
abstract class OpenScreenEvent implements Event{
    abstract String screenName();
    
    @AutoValue.Builder
    interface Builder{
       Builder screenName(String screenName);
                      
       Builder id(String id);
       Builder source(String source);
       Builder name(String name);
       Builder timestamp(Long timestamp);
       OpenScreenEvent build();
    }
}

@AutoValue
abstract class SignInEvent implements Event{
    abstract String username();
    
    @AutoValue.Builder
    interface Builder{
       Builder username(String username);
                      
       Builder id(String id);
       Builder source(String source);
       Builder name(String name);
       Builder timestamp(Long timestamp);
       SignInEvent build();
    }
}
```

This is much better. However, we must declare builder methods for all properties, including the ones declared in the ```Event``` interface. While this is a good thing, it also means we still have a lot of duplicate code in all builders.

## CRTP Builders

Let's analyze what we have and what we need. We have multiple builders with a set of common methods for which the only difference is the return type. ```SignInEvent.Builder.id()``` returns a ```SignInEvent.Builder``` instance, whereas ```OpenScreenEvent.Builder.id()``` returns a ```OpenScreenEvent.Builder``` instance, and so on. So, what we need is to somehow create an interface that will hold all the common methods, but will return the correct Builder class.

There is an easy solution for our problem that goes by the name of CRTP ([Curiously Recurring Template Pattern](https://en.wikipedia.org/wiki/Curiously_recurring_template_pattern)). According to Wikipedia, CRTP ***is an idiom \[...\] in which a class X derives from a class template instantiation using X itself as template argument***. Simply put, it says that we can have an interface like this:

```java
interface BaseBuilder<T extends BaseBuilder<T>>{
    T setterMethod(Object value);
}
```

Now we can apply this pattern to our builders. We will create a builder interface called ```BaseBuilder``` that will hold all common methods and place it in the ```Event``` interface to keep things together. Then, each specific builder will extend ```BaseBuilder``` and set itself as the interface parameter. Here's the code:

```java
// Event interface that holds common properties
interface Event {
    String id();
    String source();
    String name();
    Long timestamp();
    
    interface BaseBuilder<T extends BaseBuilder<T>>{
       T id(String id);
       T source(String source);
       T name(String name);
       T timestamp(Long timestamp);
    }
            
}

@AutoValue
abstract class OpenScreenEvent implements Event{
    abstract String screenName();
    
    @AutoValue.Builder
    interface Builder implements BaseBuilder<Builder>{
       Builder screenName(String screenName);
                     
       OpenScreenEvent build();
    }
}

@AutoValue
abstract class SignInEvent implements Event{
    abstract String username();
    
    @AutoValue.Builder
    interface Builder implements BaseBuilder<Builder>{
       Builder username(String username);
                      
       SignInEvent build();
    }
}
```

That is it. A simple trick to avoid duplicate code and to easily add/remove common properties to our events.

## Reduce even more code

The code above is probably where you would stop. But, though it is a minor improvement, my personal preference is to get rid of the ```build()``` method as well. That's because it is, more or less, a method you would copy and paste from another event class. So, I would add an extra parameter to our ```BaseBuilder``` interface that will allow us to declare the ```build()``` method generically. Our final version of the above code looks like this:


```java
// Event interface that holds common properties
interface Event {
    String id();
    String source();
    String name();
    Long timestamp();
    
    interface BaseBuilder<T extends BaseBuilder<T>, E extends Event>{
       T id(String id);
       T source(String source);
       T name(String name);
       T timestamp(Long timestamp);
       E build();
    }        
}

@AutoValue
abstract class OpenScreenEvent implements Event{
    abstract String screenName();
    
    @AutoValue.Builder
    interface Builder implements BaseBuilder<Builder, OpenScreenEvent>{
       Builder screenName(String screenName);
    }
}

@AutoValue
abstract class SignInEvent implements Event{
    abstract String username();
    
    @AutoValue.Builder
    interface Builder implements BaseBuilder<Builder, SignInEvent>{
       Builder username(String username);
    }
}
```

In conclusion, we can apply interfaces and the CRTP trick to write shorter and better organized AutoValue models.
