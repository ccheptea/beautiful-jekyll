---
layout: post
published: true
title: auto-value-variant
---

I just started a new Android project and decided to get my hands dirty with some new technologies. While doing so, I came across a very common problem that simply pisses me off (no better way to put it): **comparing fields of two objects** when filtering a list. Now, because the project I work on is boring for the general public, I will use [**wands**](http://harrypotter.wikia.com/wiki/Wand) instead. 

(TL;DR; [Show me the source code](https://github.com/ccheptea/auto-value-variant))

## A problem

Here's a Wand model:

```java
@AutoValue abstract class Wand{
    abstract String owner();
    abstract String wood();
    abstract String core();
    abstract float length();
    abstract String flexibility();
}
```
First thing to notice is that I use [AutoValue](https://github.com/google/auto/blob/master/value/userguide/index.md). This means wand objects will be immutable and the methods ``equals``, ``toString`` and ``hashCode`` will be automatically implemented in the generated class ``AutoValue_Wand``. 

Suppose you have Harry's wand and a list of other wands and you want to find all wands whose core and wood match Harry's. You could do this:

```java
List<Wand> likeHarrys = new ArrayList<>();
for(Wand wand : wands){
	if("Holly".equals(wand.wood()) && "Pheonix feather".equals(wand.core())){
    	likeHarrys.add(wand);
    }
}
```
Does it do the job? Yes. Do you like it? Hopefully not. What if you want a list ``likeHermiones`` too. For that you can write a method like the one below.

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
This one fixed it, but, sadly, there are **too many paranthesis** and **null checks**. In other words hard to read and hard to maintain. And maybe not so obviously, more null checks and paranthesis need to be added if we want to filter by other combinations of fields. For instance, for wood, core and length you will need another method ``sameWoodCoreAndLength`` with six null checks and about 36 paranthesis. Clearly, we need a better solution.

## A solution

Thankfully, our model is taken care of by ``@AutoValue``. When you start using it you get to know the magic of ``AutoValueExtension``. There are many extensions. If you haven't seen any, check Ryan Harter's [article](http://ryanharter.com/blog/2016/05/16/autovalue-extensions/). Now as I said at the beginning, I wanted to get my hands dirty. So, I wrote yet another extension called [auto-value-variant](https://github.com/ccheptea/auto-value-variant). I needed it to be easily integrated, make my code cleaner and easier to maintain. In short, I ended up this:

```java
Wand harrysWand = new AutoValue_Wand("Harry Potter", "Holly", "Pheonix feather", 11, "Nice and Supple");

for(Wand wand : wands){
	if(harrysWand.like(wand)){
    	likeHarrys.add(wand);
    }
}

```

To make this work the auto-value-variant dependency needs to be added to the project. Then transform the Wand model like this:

```java
@AutoValue abstract class Wand implements Variant{
    abstract String owner();
    @NonVariant
    abstract String wood();
    @NonVariant
    abstract String core();
    abstract float length();
    abstract String flexibility();
}
```

That is all. The interface [``Variant``](https://github.com/ccheptea/auto-value-variant/blob/master/src/main/java/com/ccheptea/auto/value/variant/Variant.java) defines the methods for comparing the objects, while the ``@NonVariant`` annotation is applied to properties we want to match when comparing them.

There's also the case when you might want multiple filters with different field combinations. For that we can define variance or similarity groups using the ``@NonVariant`` annotation. Simpler put, suppose you want to have two filters. The first one filters by the wands material (wood and core). The second one filters by their features (length and flexibility). Lets modify the model.

```java
@AutoValue abstract class Wand implements Variant{
    abstract String owner();
    
    @NonVariant("material")
    abstract String wood();
    
    @NonVariant("material")
    abstract String core();
    
    @NonVariant("features")
    abstract float length();
    
    @NonVariant("features")
    abstract String flexibility();
}
```

Then filter.

```java
for(Wand wand : wands){
	if(harrysWand.like(wand, "material")){
    	likeHarrysWandMaterial.add(wand);
    }
    
    if(harrysWand.like(wand, "features")){
    	likeHarrysWandFeatures.add(wand);
    }
}
```

Using streams is even better.

```java
likeHarrysWandMaterial = wands.stream().filter(wand -> harrysWand.like(wand, "material"));
likeHarrysWandFeatures = wands.stream().filter(wand -> harrysWand.like(wand, "features"));
```

Notice how the comparison reads almost naturally: _is Harry's wand like wand material?_ ~= ``harrysWand.like(wand, "material")``.

Some variance groups may share properties. Suppose you're interested in wands similar to Harry's in terms of length and core. In this case, use a string array as the value for ``@NonVariant``.

```java
@AutoValue abstract class Wand implements Variant{
    abstract String owner();
    
    @NonVariant("material")
    abstract String wood();
    
    @NonVariant({"material", "length_and_core" })
    abstract String core();
    
    @NonVariant({"features", "length_and_core"})
    abstract float length();
    
    @NonVariant("features")
    abstract String flexibility();
}
```
## A conclusion

Code should be as clean and maintainable as possible. With ``AutoValue``, in most cases, those are guaranteed. Using its extensions makes it even better, boosting up development and eliminating the need for plenty of cumbersome tests. [``auto-value-variant``](https://github.com/ccheptea/auto-value-variant) is one of those extensions by helping you to easily and comprehensibly define static filters.