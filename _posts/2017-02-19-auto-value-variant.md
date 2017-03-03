---
layout: post
published: true
title: auto-value-variant
---

I just started a new Android project and decided to get my hands dirty with some new technologies. Most of you will know exactly what I mean. While doing so, I came across a very common problem that simply pisses me off (no better way to put it): **comparing fields of two objects**. Now, because the project I work on is boring for the general public, I will use [**wands**](http://harrypotter.wikia.com/wiki/Wand) instead. So, here's a Wand model:

```java
@AutoValue abstract class Wand{
    abstract String owner();
    abstract String wood();
    abstract String core();
    abstract float length();
    abstract String flexibility();
}
```
First thing to notice is that I use [AutoValue](https://github.com/google/auto/blob/master/value/userguide/index.md). This means wand objects will be immutable and the methods ``equals``, ``toString`` and ``hashCode`` will be automatically implemented in a generated class ``AutoValue_Wand``. 

## A problem

Suppose you have Harry's wand and a list of other wands. You want to find all wands whose core and wood match Harry's wand. You could do this:

```java
List<Wand> likeHarrys = new ArrayList<>();
for(Wand wand : wands){
	if("Holly".equals(wand.wood()) && "Pheonix feather".equals(wand.core())){
    	likeHarrys.add(wand);
    }
}
```
Does it do the job? Yes. Do you like it? Hopefuly not. What if you want a list ``likeHermiones`` too. For that you can write a method like the one below.

```java
public boolean sameWoodAndCore(String wand1, String wand2){
	return (wand1.wood() == null ? wand2.wood() == null : wand1.wood().equals(wand2.wood()))
    	&& (wand1.core() == null ? wand2.core() == null : wand1.core().equals(wand2.core()))
}

...

Wand hpWand = new AutoValue_Wand("Harry Potter", "Holly", "Pheonix feather", 11 "Nice and Supple");

for(Wand wand : wands){
	if(sameWoodAndCore(hpWand, wand)){
    	likeHarrys.add(wand);
    }
}

```
This one fixed it, but, sadly, there are **too many paranthesis** and **null checks**. In other words hard to read and hard to maintain. And maybe not so obviously, more null checks and paranthesis need to be added if we want to filter by other combinations of fields. For instance, for wood, core and length you will need another method ``sameWoodCoreAndLength``.

```java
Wand hWand = new AutoValue_Wand("Harry Potter", "Holly", "Pheonix feather", 11 "Nice and Supple");


```

## A solution
