---
layout: post
published: true
title: auto-value-variant
---

I just started a new Android project and decided to get my hands dirty with some new technologies. Most of you will know exactly what I mean. While doing so, I came across a very common problem that simply pisses me off (no better way to put it): **comparing fields of two objects**. Now, because the project I work on is boring for the general public, I will use [**wands**](http://harrypotter.wikia.com/wiki/Wand) instead. So, here's a Wand model:

```java
@AutoValue abstract class Wand{
    abstract String owner();
    abstract String flexibility();
    abstract float length();
    abstract String core();
    abstract String wood();
}
```
First thing to notice is that I use [AutoValue](https://github.com/google/auto/blob/master/value/userguide/index.md). This means wand objects will be immutable and the methods ``equals``, ``toString`` and ``hashCode`` will be automatically implemented in a generated class ``AutoValue_Wand``. 


## A solution