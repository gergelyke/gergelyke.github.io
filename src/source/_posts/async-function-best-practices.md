---
title: Node.js Async Function Best Practices
description: Mastering async functions in Node.js - error handling, parallel execution and complex control flows
githubIssueId: 5
date: 2017-10-24
tags:
    - 'node.js'
    - 'best practices'
    - 'async await'
---

Since Node.js version 7.6, Node.js ships with a new V8 version that features async functions. As [Node.js 8 becomes the active LTS version on October 31](https://github.com/nodejs/Release), there is no reason for not starting to adopt async functions in your codebase. In this article I will briefly show you what async functions are, and how they change the way we write Node.js applications.

# What are `async` functions?

`async` functions let you write `Promise`-based code as if it were synchronous. Once you define a function using the `async` keyword, then you can use the `await` keyword within the function's body. When the `async` function is called, it returns with a `Promise`. When the `async` function returns a value, the `Promise` gets fulfilled, if the `async` function throws an error, it gets rejected.

The `await` keyword can be used to wait for a `Promise` to be resolved and returns the fulfilled value. If the value passed to the `await` keyword is not a Promise, it converts the value to a resolved `Promise`. 

```javascript
const rp = require('request-promise')

async function main () {
  const result = await rp('https://google.com')
  const twenty = await 20
  
  // sleeeeeeeeping  for a second 💤
  await new Promise (resolve => {
    setTimeout(resolve, 1000)
  })

  return result
}

main()
  .then(console.log)
  .catch(console.error)
```

# Migrating to `async` functions

If your Node.js applications are already using `Promise`s, then you only have to start `await`ing your Promises, instead of chaining them.

If your applications are built using callbacks, moving to `async` functions should be done gradually. You can start adding new features by using this new technique. If you have to use older parts of the application, you can simply wrap them into Promises.

To do so, you can use the built-in `util.promisify` method:

```javascript
const util = require('util')
const {readFile} = require('fs')

const readFileAsync = util.promisify(readFile)

async function main () {
  const result = await readFileAsync('.gitignore')
  return result
}

main()
  .then(console.log)
  .catch(console.error)
```

# Best Practices for `async` functions

## Using `async` functions with `express`

As `express` supports Promises out of the box, using `async` functions with express is as simple as:

```javascript
const express = require('express')
const app = express()

app.get('/', async (request, response) => {
  // awaiting Promises
  const result = await getContent()

  return result
})

app.listen(process.env.PORT)
```

## Parallel execution

Imagine you are working on something similar, when an operation needs two inputs, one from a database, and one from an external service:

```javascript
async function main () {
  const user = await Users.fetch(userId)
  const product = await Products.fetch(productId)

  await makePurchase(user, product)
}
```

In this case, what will happen is the following:

* your code will first get the `user` resource,
* then get the `product` resource,
* and finally make the purchase.

As you can see, you can do the first two in parallel, as they have no dependency on each other. For this, you should use the `Promise.all` method:

```javascript
async function main () {
  [user, product] = await Promise.all([
    Users.fetch(userId),
    Products.fetch(productId)
  ])

  await makePurchase(user, product)
}
```

In some cases, you only need the result of the fastest resolving Promise - in that cases, you can use the `Promise.race` method.

## Error handling

Consider the following code example:

```javascript
async function main () {
  await new Promise((resolve, reject) => {
    reject(new Error('💥'))
  })
}

main()
  .then(console.log)
```

Once running this snippet, you will get a message in your terminal saying something similar:

```bash
(node:69738) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 2): Error: 💥
(node:69738) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.
```

In the newer versions of Node.js, if Promise rejections won't be handled, it will bring down the whole Node.js process. Because of this, you should  use `try-catch` blocks, when necessary:

```javascript
const util = require('util')

async function main () {
  try {
    await new Promise((resolve, reject) => {
      reject(new Error('💥'))
    })
  } catch (err) {
    // handle error case
    // maybe throwing is okay depending on your use-case
  }
}

main()
  .then(console.log)
  .catch(console.error)

```

## More complex control flows

One of the first async control flow libraries for Node.js was the one called [async](http://caolan.github.io/async/) created by [Caolan McMahon](https://twitter.com/caolan). It provides asynchronous helpers, like:

* `mapLimit`,
* `filterLimit`,
* `concatLimit`,
* or `priorityQueue`.

If you do not want to reinvent the wheel and implement the same logic again, and you also want to depend on a battle-tested library downloaded 50 million times a month, you can simply reuse these functions with `async` functions as well, using the `util.promisify` method:

```javascript
const util = require('util')
const async = require('async')

const numbers = [
  1, 2, 3, 4, 5
]

mapLimitAsync = util.promisify(async.mapLimit)

async function main () {
  return await mapLimitAsync(numbers, 2, (number, done) => {
    setTimeout(function () {
      done(null, number * 2)
    }, 100)
  })
}

main()
  .then(console.log)
  .catch(console.error)
```

# Next up

I hope you enjoyed this article and learned a lot! 👩‍🎓👨‍🎓 If you'd like to get my latest articles right into your mailbox, you can [sign up here](https://www.getrevue.co/profile/gergelyke) to my newsletter 📨.

Also, if there is anything you'd like to learn more about and read on this blog, just let me know in the comments sections, or drop me a message on [twitter](https://twitter.com/nthgergo)!
