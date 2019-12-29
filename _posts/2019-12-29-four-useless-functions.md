---
layout: post
published: true
title: 4 useless functions I wish JavaScript offered out of the box
date: '2019-12-29'
tags:
  - javascript
  - indexOf
  - binarySearch
  - range
  - distinct
  - prototype
---

JavaScript, [the most popular programming language in the world](https://insights.stackoverflow.com/survey/2019#technology), is a mess. There are many articles (or [repositories](http://bit.ly/wtfjavascript)) on the web about how peculiar or counter-intuitive some aspects of this language are. But as much as you may hate it, you have to admit it is a handy tool if you want to quickly build a small project, prove some code works, or simply have some fun. I think most of its critics loved the language at some point in their lives, but are now too cool or sophisticated to admit it. I, personally, use it a lot for solving algorithmic problems from websites such as HackerRank or ProjectEuler.

But even if JavaScript is so widely and wildly used, sometimes it feels incomplete and forces you to break a function chain or implement a basic functionality from the ground up. Here are four functions that I wish were readily available in JavaScript. I will provide usage examples and possible implementations for each of them.


## Array.distinct()

Do a simple search on how to get the distinct values in a collection in JavaScript and you will find plenty of results from StackOverflow to blogs and tutorials. There is no doubt this is a commonly needed functionality. So, why isn't it built-in? Kotlin, for instance, has two distinct methods: `distinct` and `distinctby`. The latter allows you to get distinct elements by a specific property. 

Here's how I see the `.distinct()` method in JavaScript.

### Usage

``` javascript
[1, 2, 3, 1, 2, 3]
    .distinct()
    .forEach(x => console.log(x)) 
/*
output:
1
2
3
*/

[{name: "Bob", name: "Jane", name: "Bob"}]
    .distinct(person => person.name)
    .forEach(x => console.log(x)) 
/*
output:
{ name: 'Bob' }
{ name: 'Jane' }
*/

```

### Possible implementation

```javascript
Array.prototype.distinct = function(selector){
  if (selector === 'undefined'){
    return [...new Set(this)];
  }

  if (typeof(selector) !== 'function'){
    throw new Error(`Expecting selector to be a function, but received ${typeof(selector)} instead.`)
  }
  
  let found = new Set()
  return this.filter(element => {
    if (found.has(selector(element))){
      return false
    } else {
      found.add(selector(element))
      return true
    }
  })
}
```

## Array.indicesOf()

You're familiar with `.indexOf()`. It returns the index of the first appearance for a given value, or `-1` if no match is found. Surprisingly, I encountered situations where I needed to determine not just the first index, but all of them. It can be done with a `.reduce()`, but then I need to explain what I'm trying to achieve. I'm not a big fan of comments in code. Thus, although it seems a useless function, for the sake of expressiveness I would like to have it out of the box.

### Usage 
```javascript
[1, 2, 3, 1, 2, 3]
    .indicesOf(2)
    .forEach(x => console.log(x)) 
/*
output:
1
4
*/

[{name: "Bob", name: "Jane", name: "Bob"}]
    .indicesOf(person => person.name)
    .forEach(x => console.log(x)) 
/*
output:
0
2
*/
```

### Possible implementation

```javascript
Array.prototype.indicesOf = function(test){
  if(typeof(test) === "function"){
    return this.reduce((indices, element, index) => {
      if (test(element)) indices.push(index)
      return indices
    }, [])
  } else {
    return this.reduce((indices, element, index) => {
      if (element === test) indices.push(index)
      return indices
    }, [])
  }
}
```

## Array.binarySearch()

Similar to `.distinct()`, running a binary search may be something you would like to use more often but cannot without some extra work. Binary search looks for a given element in a sorted array and returns it's index, or `-1` if missing. It does the same thing as `.indexOf()` but a lot faster. Since it is such a well known and basic algorithm, it makes a lot of sense to have it built-in along with the sorting algorithms. I was pleased to find it in Kotlin. Kudos to the Kotlin team.

### Usage

```javascript
let index = [1, 2, 3, 4, 5, 5, 7].binarySearch(x => x - 7)
console.log(index)
/*
output:
6
*/
```

### Possible implementation

```javascript
Array.prototype.binarySearch = function(comparator){
  let left = 0, right = this.length - 1
  while(left <= right){
    let mid = Math.floor((right + left)/ 2)
    let cmp = comparator(this[mid])
    if(cmp < 0){
      left = mid + 1
    } else if(cmp > 0){
      right = mid - 1
    } else{
      return mid
    }
  }
  return -1
}
```

## Number.rangeTo()

You may find generating a list of integers a useful functionality. It can be sample data for your tests, or coordinates for your cool game. You can do it using a `for` loop, but smarter languages have the concept of range. Here's how one can define a range of integers in Swift: `let myRange = 1..10`. Now `myRange` can be used as a collection of integers that you can manipulate as you would a good old array. JavaScript doesn't provide this, but it seems like a common enough feature that should be part of any programming language.

### Usage

```javascript
let myRange = (1).rangeTo(10)
console.log(myRange)
/*
output:
[ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 ]
*/
let myRange2 = (-3).rangeTo(3)
console.log(myRange2)
/*
output:
[ -3, -2, -1, 0, 1, 2, 3 ]
*/
```

### Possible implementation
```javascript
Number.prototype.rangeTo = function (toValue){
  if (!Number.isInteger(this.valueOf()) || !Number.isInteger(toValue))
    throw new Error("Only integers are allowed!")

  let values = new Array()
  for(i = this.valueOf(); i <= toValue; i++){
    values.push(i)
  }
  return values
}
```

These useless functions are just the ones I recently needed, but surely there are plenty others that should make it in this list. What else do you think is worth adding?






