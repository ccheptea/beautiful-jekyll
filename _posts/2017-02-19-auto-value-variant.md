---
layout: post
published: true
title: auto-value-variant
---
## A problem


I just started a new Android project and decided to get my hands dirty with some new technologies. Most of you will know exactly what I mean. While doing so, I came across a very common problem that pisses me off (no better way to put it): **comparing fields of two objects**. Now, because the project I work on is boring for the general public, I will use [**wands**](http://harrypotter.wikia.com/wiki/Wand) instead. So, here's a class:

```java
public abstract class Wand{
    abstract String owner();
    abstract String flexibility();
	abstract String wood();
    abstract float length();
    abstract String core();
}
```



## A solution