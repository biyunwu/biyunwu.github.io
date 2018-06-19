---
layout:       post
title:        "Idetify `this` In JavaScript"
subtitle:     ""
date:         2018-02-19 12:00:00
author:       "Biyun Wu"
header-img:   "img/js.jpg"
header-mask:  0.3
catalog:      true
multilingual: false
tags:
    - JavaScript
    - ES6
    - Tricks
---

> Only One Rule: The value of `this` in a function is set once the function is invoked.
>
> Three Situations: Normal Situation, Special Functions (`.bind()` `.call()` `.apply()`...) and Arrow Function.

# One Rule and Three Categorialized Situations to Identify `this` in Javascript (ES6).

## **Situation 1**: The exact value of `this` is set once a method is invoked

```js
// Constructor function
function Cat ({age=6, color='yellow'}={}){
    this.age = age;
    this.color = color;
}

// Add the info() method
Cat.prototype.info = function(){
    console.log(`Meow! Age: ${this.age}, color: ${this.color}.`);
}

// Create a Cat object named cat
const cat = new Cat({age:3, color:'black'});
// Return cat's 'age' property
cat.age;    // 3
// Invoke a method on cat
cat.info(); // 'Meow! Age: 3, color: black.'
```

In the example, after the object of `cat` is created, its value eqauls to:

```js
{age: 3, color: "black"}
```

When the `.info()` method is invoked on `cat`, the value of `this` is set to `cat`, so that the `.info()` method in the constructor function's prototype can access the `cat`'s `age` and `color` properties.

## **Situation 2**: `this` with `.call()`, `.apply()` and `.bind()`

```js
const sumObj = {
    array: [1, 2, 3, 4, 5],
    sum: function(string){
        let total = 0;
        for(const num of this.array){
            total += num;
        }
        return `${string} ${total}`;
    }
};

const numObj = {
    array: [6, 7, 8, 9, 10]
};
sumObj.sum('Sum:');            // Sum: 15
sumObj.sum.call(numObj, ['Total:']); // Total: 40
```

In the sample code, `sum` is a property of `sumObj`, when the `.call()` method is invoked on it with the parameters of `numObj` and the string, the value of `this` in the `.sum()` funtion is set to `numObj`. So, the `.sum()` method can be called on `numObj`.

Both `.apply()` and `.bind()` are similair to `.call()`, they are called on functions and given an object and the functions' arguments. So, `this` in functions can be referred to and called on the given object.

Even though `.call()`, `.apply()` and `.bind()` are not categorized in the **Situation 1**, but they also comply the rule that `this` refers to the object which the function is invoked on.

## **Situation 3**: `this` in arrow function

The reason why `this` with arrow functions should be taken into special consideration is that, the scope of arrow function is different from regular function.

```js
this.num = 0;

const obj1 = {
    num: 10,
    add: function(){
        this.num++;
        console.log(this, this.num);
    }
};

const obj2 = {
    num: 20,
    add: () => {
        this.num++;
        console.log(this, this.num)
    }
}

obj1.add(); // {num: 11, a: 2, add: ƒ} 11
obj2.add(); // Window {frames: Window, postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, …} 1
```

After arrow funtion `.add()` invoked on `obj2`, the value of `this` was set to the `window` object. As [Ben Garrison](https://hackernoon.com/javascript-es6-arrow-functions-and-lexical-this-f2a3e2a5e8c4) mentioned, `this` in a arrow function is exactly "lexical this" which means `this` refers to the function's upper level code's `this`. 

When `obj2.add()` executes, `this` refers to `obj2`'s upper level `this`, which is the `window` object.

This situation can be a hassal in ES6. However, considering arrow function provide a convenient way to reach the function's upper level scope, it is also very useful.

All in all, The one and only rule is that the value of `this` in a funciton is set once the function is invoked.