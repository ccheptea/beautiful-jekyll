---
layout: post
published: true
title: auto-value-variant
---
## A problem


I just started a new Android project and decided to get my hands dirty with some new technologies. Most of you will know exactly what I mean. While doing so, I came across a very common problem that pisses me off (no better way to put it): **_comparing fields of two objects_**. Now, because the project I work on is boring for the general public, I will use [**_wands_**](http://harrypotter.wikia.com/wiki/Wand) instead. So, here's a class:

{% highlight java%}
abstract class Wand{
	abstract String wood();
    abstract float length();
    abstract String core();
    abstract String flexibility();
    abstract String owner();
}
{% endhighlight%}



## A solution