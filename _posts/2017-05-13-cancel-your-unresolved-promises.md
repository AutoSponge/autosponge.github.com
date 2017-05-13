---
published: true
layout: post
category: blog
tags: [javascript, promises, async]
---

The halting problem; Promises don't make it easy.

If you like the idea of `async/await`, then you have to be all in on
Promises. Lots of frameworks embrace the new paradigm and it's truly
a delight to program in--most of the time.

You can't cancel a Promise. That's the hardest thing to deal with if
you create a procedure with a Promise that will never resolve or
if you want to stop a long-running process inside that procedure.

Luckily, the garbage collection seems to take care of the objects and
listeners created. So, we only need to concern ourselves with how
to cancel the rogue Promise's process. Consider the following example:

```js
Promise.race([process1(), process2()])
  .then((result) => /* do something */)
  .catch(/* handle errors */)

```

If the Promise returned by `process1` resolves and we want to cancel
the Promise returned by `process2` we're out of luck. We don't even
get a reference to the Promise `process2` creates, we only get the
result of `process1`'s `resolve`.

We need an abstraction. This idea is borrowed from [`pump`](https://github.com/mafintosh/pump)
while dealing with a similar issue in streams. When one stream closes,
you want to close all of them and `pipe()` doesn't do that for you.

Instead of calling `Promise.race()` we need to create our own `race()`
function so we can `abort()` any cancelable Promises in the `then()`
handler. It should look like this:

```js
race(cancelablePromise(), regularPromise(), cancelablePromise())
      .then((resolvedValue) => handleResolvedValue)
      .catch((rejectOrError) => handleRejectOrError)
```

Here's my implementation:

```js
const send = name => obj => {
  if (typeof obj === 'object' && typeof obj[name] === 'function') {
    obj[name]()
  }
}
const race = (...promises) => {
  return Promise.race(promises)
    .then((result) => {
      promises.forEach(send('abort'))
      // make sure we forward the resolve value
      return result
    })
    .catch((err) => {
      promises.forEach(send('abort'))
      // make sure we throw again
      throw err
    })
}
```

Now we need a Promise we can actually cancel by calling `abort` but
keep all the semantics of native Promises.

```js
const cancelablePromise = () => {
  let abort
  const internal = new Promise((resolve, reject) => {
    /* some internals */
    abort = () => reject()
  })
  // as an expando property
  internal.abort = abort
  return internal
}
```

Ideally, we wouldn't call `abort()` until a parallel process finishes.
At that time, the value of our `cancelablePromise` doesn't matter. But,
just in case, we don't want to use `resolve`. If we're worried about
tampering, we could also use `Object.defineProperty()`.

This isn't clean because we need the actual `reject` method injected
by the internal Promise. So, we can't pass in our own resolver. But
with a little es6 class-fu, we can make a class that seems more
natural.

```js
class CancelablePromise extends Promise {
  constructor(resolver, onAbort = () => {}) {
    let handle
    super((resolve, reject) => handle = {resolve, reject})
    resolver.call(null, handle.resolve, handle.reject)
    Object.defineProperty(this, 'abort', {
      enumerable: false,
      writable: false,
      configurable: false,
      value: () => {
        handle.reject('aborted')
        onAbort()
        return this
      }
    })
  }
}
```

Let's put it together:

```js
let interval
const promise1 = () => new CancelablePromise(() => {
  // will not resolve on its own
  interval = setInterval(() => {console.log('Promise 1 still going')})
}, () => clearInterval(interval))

let timeout
const promise2 = () => new CancelablePromise((resolve) => {
  timeout = setTimeout(() => {
    console.log('resolved Promise 2')
    resolve(2)
  }, 1000)
}, () => clearTimeout(timeout))

const promise3 = () => new Promise((resolve, reject) => setTimeout(() => {
  // will resolve but can't be aborted early
  console.log('resolved Promise 3')
  resolve(3)
}, 1500))

race(promise1(), promise2(), promise3())
  .then((resolvedValue) => console.log({resolvedValue}))
  .catch((caughtError) => console.error({caughtError}))
```

My output looks something like this:

```bash
(252) Promise 1 still going
resolved Promise 2
Object {resolvedValue: 2}
resolved Promise 3
```

This `race()` calls `abort()` on the cancelable promise once our process
resolves. Sadly, it can't do anything to stop promise3 so it keeps
going.

Let's try it with an error scenario:

```js
const promise4 = () => new Promise((resolve, reject) => {
  setTimeout(() => reject(4), 500)
})
race(promise1(), promise2(), promise3(), promise4())
  .then((resolvedValue) => console.log({resolvedValue}))
  .catch((caughtError) => console.error({caughtError}))
```

Now my output looks like this:

```bash
(128) Promise 1 still going
Object {caughtError: 4}
resolved Promise 3
```

Promise1 and Promise2 abort early and we still catch the rejection.