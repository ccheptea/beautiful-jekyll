---
layout: post
published: false
title: Deduce how to Array.reduce()
subtitle: >-
  Some simple to complex examples and exercises to easily understand how
  Array.reduce() works
---

`Array.reduce()` is not easy to understand. I say this after several training sessions about Array functions in JavaScript. It isn't hard to see why this one in particular is substantially harder to grasp. Let's compare it to `.filter()` and `.map()` and try to see why that is.

### Filter

``` js
// Example: 
[1, 2, 3, 4].filter(x => x > 2) // result: [3, 4]
```
- The name is very intuitive, you use it to filter out some elements that you don't need.
- The callback function will always return `true` or `false`.
- The end result will always be an array with unchanged elements and at most the same length. 

### Map

``` js
// Example: 
[1, 2, 3, 4].map(x => x * x) // result: [1, 4, 9, 16]
```
- The name is intuitive, you use it to transform all the elements in the array.
- The callback function will (ideally) always return a modified version of each element.
- The end result will always be an array of the same length with the elements modified.

### Reduce

```
// Example 1:
[1, 2, 3, 4].reduce((acc, x) => acc + x, 0) // result: 10

// Example2:
[1, 2, 3, 4].reduce((acc, x) => acc + x, '') // result: "1234"

// Example3: 
[1, 2, 3, 4].reduce((acc, x) => acc[x] = x*x, {}) // result: { '1': 1, '2': 4, '3': 9, '4': 16 }
```

- The name advertises that you will somehow reduce all elements to a single value. That may seem intuitive when you want to calculate a sum, but isn't when determining the frequences of all elements when you will end .
- The callback has an extra parameters: the accumulator, which needs to be combined with each element and the result will be passed over as the new accumulator in the next iteration. It isn't clear how this accumulator is stored and how it manages to survive the entire process.
- The end result can be, literally, anything you want: a number, a string, a list, a map, etc.


