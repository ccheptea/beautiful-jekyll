---
layout: post
published: false
title: auto-value-node
subtitle: Safely and gracefuly access optional fields
---
At [Tango Targeting](http://tangotargeting.com/) we try to keep our code as simple and comprehensible as possible. One of the challenges in doing so is having a complex and deep data structure. And by deep I mean something like in the following snippet:

```java
String email = car.driver().club().contact().email();
if(email != null)
	System.out.println(email);
```
Nothing fancy here: an email is printed and you may agree that everything is easy to read and understand. But did you spot any problems? If not, take another moment and try to identify the  devil hidden in the chain.

(TL;DR; [Show me the source code](https://github.com/ccheptea/auto-value-node))


## Safe code is not always elegant code

Working with data teaches you that it can very often be missing. Not every car has a driver. A driver may not be a member of a driving club. A club may choose not to list any contact details. If any of these apply, our code will break with a ``NullPointerException``.

This can be easily remedied with some null checks.

```java
if(car.driver() != null && car.driver().club() != null && car.driver().club().contact() != null){
	String email = car.driver().club().contact().email();
    if(email != null)
		System.out.println(email);
}
```
This is safe but not simple anymore. Reading becomes difficult and so is maintaining. 


## Using Optionals
Luckily, Java 8 comes with Optionals. An [Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html) allows you to do something with a value depending on whether it exists or not. 

```java
Optional.of(car).ifPresent(value -> doSomethingWitCar(value)));
```

If Java 8 is not an option for you yet (us included), [this little project](https://github.com/aNNiMON/Lightweight-Stream-API) may cover for it. As explained [here](http://www.deadcoderising.com/2015-10-06-java-8-removing-null-checks-with-optional/) we can try to improve our code:

```java
Optional.of(car).map(Car::driver).map(Driver::club).map(Club::contact).map(Contact::email)
	.ifPresent(System.out::println);
```

This is a little better. However, we access our data a lot and having ``map`` and double colons (::) all over the place, makes me think of a quacking duck trying to survive John Conway's [Game of Life](https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life). So how can we make this code look more elegant and equally safe? We figured it should resemble its original form but magically avoid crashing and, to some extent, resemble Optionals. Hence we turned our heads toward [AutoValue](https://github.com/google/auto/tree/master/value) and more specifically its extensions.

## Using AutoValue Node Extension

We use [AutoValue](https://github.com/google/auto/tree/master/value) for maintaining our models and, so far, we're grateful for it. With several AutoValue Extensions our models are taken care of. We developed another extension: [auto-value-node](https://github.com/ccheptea/auto-value-node). Using it, we can can transform the code above in the following manner:

```java
String email = car.node().driver().club().contact().email().value();
if(email != null)
	System.out.println(email)
```
And it looks very similar to what we started with. But, actually, it can get even better.

```java
car.node().driver().club().contact().email()
	.ifPresent(System.out::println);
```
Printing a message if the email is missing is just as simple.

```java
car.node().driver().club().contact().email()
	.ifPresent(System.out::println)
    .otherwise(() -> System.out.println("The email is missing"));
```

So, we've completely gotten rid of an if-else block by replacing it with a more elegant and expressive structure. And above all, it is safe - null checks are implicitly handled by the extension.

#### 

