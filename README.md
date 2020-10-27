## `Promise.uncaught`

ECMAScript Proposal, specs, and reference implementation for `Promise.uncaught`

Spec drafted by [@Aleen](https://github.com/aleen42).

This proposal is currently [stage 1](https://github.com/tc39/proposals/) of the [process](https://tc39.github.io/process-document/).

### Motivation

- To set up a default handler for any promises without catching the result of rejection, like what [`unhandledRejection`](https://nodejs.org/api/process.html#process_event_unhandledrejection) has done in Node.
- Read enhancement corresponding to `Promise.prototype.uncaught` discussed in [the group](https://es.discourse.group/t/promise-prototype-uncaught/507/3)?

    In the following snippet, `Promise.prototype.catch` has the same desired result.

    ```js
    const handler = () => console.log('uncaught'), caught = r => r > 1, ignore = () => {};
    Promise.reject(1).uncaught(handler); // => "uncaught"
    Promise.reject(1).uncaught(handler).catch(caught); // => "uncaught"
    Promise.reject(1).catch(caught).uncaught(handler); // => won't fired as already been caught
    Promise.reject(1).then(ignore, caught).uncuaght(handler); // => won't fired as already been caught  
    ```

### Syntax

```
Promise.uncaught(promise, handler); 
```

#### Parameters

- `promise`: the promise you want to handle uncaught situations.
- `handler`: the callback function called when the promise has not uncaught rejections. Any result returned from this function won't change the state of the whole promise chain.

#### Returns

Returns a `Promise`, which can be used for next step of the whole promise chain.

### Usage

```js
const catchCode = (expected, cb) => code => code !== expected ? Promise.reject(code) : cb();
const promise = async () => { /* may reject any code ... */ return Promise.reject(anyCode); };

Promise.uncaught(promise, () => {
    /* ... default handler for any other code */
    console.log('catch any other unknown code');
})
    .catch(catchCode(1, () => console.log('catch 1')))
    .catch(catchCode(2, () => console.log('catch 2')));    
```
