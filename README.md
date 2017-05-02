# JavaScript Promises

## Outline

1. Discuss Callbacks to Events
2. Demo Callback Hell
3. Discuss Promise history / show promise syntax
4. Build a method that returns a Promise

## Objectives

+ Use `.then` syntax in place of success callbacks
+ Wrap async events in a our own promise

## Lesson

By now, you've seen that much of JavaScript is based in events. Because our code is running asynchronously, we can't always be sure how long things will take to finish. So far, we've dealt with this using callback functions. A callback is just a function that we call once another procedure is completed. For example, `setTimeout` is a JavaScript function that takes two arguments, a function and an amount of time in milliseconds. Once that amount of time has gone by, they call the function for us.

```javascript
setTimeout(function(){
  console.log("This will be called after two seconds!")
}, 2000)
```

Let's pretend that we want to write a function that multiplies two numbers together, but that calculation takes two seconds. We'll use the setTimeout function to fake this.

```javascript
function doSlowMath(num1, num2){
  setTimeout(function(){
    num1 * num2
  }, 2000 )
}
```
Because it takes two seconds to actually do the math, I can't use the return value right away. One solution to this is to use a callback function. `doSlowMath` can accept a function definition to run whenever our slow calculation is finished.

```javascript
function doSlowMath(num1, num2, fn){
  setTimeout(function(){
    let value = num1 * num2
    fn(value)
  }, 2000 )
}
```

Now, when we call `doSlowMath`, we can pass through what we want to do with the result.

```javascript
function doSlowMath(num1, num2, fn){
  setTimeout(function(){
    let value = num1 * num2
    fn(value)
  }, 2000 )
}

doSlowMath(2, 2, (answer) => {
  return `It took awhile, but 2 times 2 is ${answer}`
})
```

The problem is, what if we want to do more slow math with the answer? We'll have to next a callback function inside of a callback function.

```javascript
function doSlowMath(num1, num2, fn){
  setTimeout(function(){
    let value = num1 * num2
    fn(value)
  }, 2000 )
}

doSlowMath(2, 2, (answer) => {
  doSlowMath(answer, 5, (newAnswer) => {
    return `The answer to 2 times 2 times 5 is ${newAnswer}`
  })
})
```

Yikes. This is super unreadable. It's hard to parse through these different function definitions and to figure out what's going to happen in what order.

## There is a better way, I Promise

Instead of having our `doSlowMath` function take in a callback, we can have it return a Promise object. Promises are native javascript objects that wrap around slow procedures. Our promises will respond to a method called `then` which will fire a function whenever a procedure has finished.

To start out, we can instantiate a new `Promise` and pass in a function. This function will take two callback functions as arguments - one for if our procedure works and one for if it doesn't work. We'll refer to these as `resolve` and `reject`.

```javascript
let promise = new Promise(function(resolve, reject){

})
```

Now, we can run our procedure and pass the result into the `resolve` function.

```javascript
function doSlowMath(num1, num2){
  return new Promise((resolve, reject) => {
    setTimeout(function(){
      let value = num1 * num2
      resolve(value)
    }, 2000 )
  })
}
```

Defining this is pretty much the same as taking in our callback function. The difference is that we'll pass our callback function into the `then` method.

```javascript
doSlowMath(2, 2)
  .then(function(answer){
    console.log(`The answer is ${answer}`)
  })
```

Promises remain in a pending state until either the resolve or reject functions get called. Once one of those fires, it can fire off the function that we pass into `then`. The cool part is that `then` returns another Promise in a pending state. This means that we can chain these calls together - the argument to the next function will be the return value of the previous one.

```javascript
doSlowMath(2, 2)
  .then(function(answer){
    return answer + 10
  })
  .then(function(answer){
    return answer - 3
  })
  .then(function(answer){
    let newAnswer =  answer + 7
    console.log(newAnswer)
  })
```

This is much easier to read - we can see the order in which things will happen now. Using ES6 arrow functions makes this much shorter as well.

```javascript
doSlowMath(2, 2)
  .then(answer => answer + 10)
  .then(answer => answer - 3 )
  .then(answer => answer + 7 )
  .then(answer => console.log(answer))
```

## Resources

+ [JavaScript Promises - A Primer ](https://developers.google.com/web/fundamentals/getting-started/primers/promises)
+ [Promises in Wicked Detail](http://www.mattgreer.org/articles/promises-in-wicked-detail/#this-code-is-brittle-and-bad)
