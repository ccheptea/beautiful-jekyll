---
layout: post
published: false
title: 4 useless functions I wish JavaScript offered out of the box
subtitle: >-
  Map starting with the second element. Reduce a specific array interval. Get
  all indices of an element in an array.
---

JavaScript is a mess. There are countless articles on the web about how peculiar or counter intuitive some aspects of this language are. But as much as you may hate it, you have to admit it is a handy tool if you want to quickly build a small project, prove some code works, or simply have some fun. I use it a lot for solving algorithmic problems from websites such as HackerRank or ProjectEuler. 

But even if JavaScript is so widely and wildly used, sometimes it feels incomplete and forces you to break a function chain or implement a basic functionality from the ground up. Here are four functions that I wish were readily available in JavaScript. I will provide usage examples and possible implementations for each of them.

## Array.distinct()

Do a simple search on how to get the distinct values in a collection in JavaScript and you will find plenty of results from StackOverflow to blogs and tutorials. There is not doubt this is a commonly needed functionality. So, why isn't it built-in? Kotlin, for instance has two distinct methods: `distinct` and `distinctby`. The latter allows you to get distinct elements by a specific property. 

Here's how I see the distinct method in JavaScript.

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



## Array.binarySearch()



## Number.rangeTo()




