# Promises

What is a JavaScript Promise? A Promise is a container for handling the results of an asynchronous operation. You can think of it as a receipt for future content. Promise libraries have been around for awhile, but with the release of ES6 they are now natively part the EcmaScript specification.  

## Synchronous and Asynchronous

Before we can dig into Promise syntax we will quickly review the difference between `synchronous` and `asynchronous` operations. Synchronous runs in real time, right now, and has control of the thread in the event loop. Asynchronous is code that will run at some unknown time in the future. We typically communicate with our future code through callbacks.  

## Callback challenge

We need to have a solid understanding of callback communication. To assist in this matter I present you with a challenge.  

```javascript
function orderSupplies(item, callback) {
  let warehouse; //undefined
  const deliveryTime = Math.random() * 3000;

  setTimeout(() => {
    warehouse = {
      paint: {
        product: 'Neon Green Paint',
        directions: () => 'mix it!'
      },
      brush: {
        product: 'Horsehair brush',
        directions: () => 'start painting!'
      }
    };

    callback(warehouse[item]);
  }, deliveryTime);
}

function receivedItem(item) {
  console.log(`Received ${item.product}, time to ${item.directions()}`);
}

orderSupplies('brush', receivedItem);
orderSupplies('paint', receivedItem);
```

Here we have a function, `orderSupplies`, which receives an item to retrieve from our warehouse and a way to communicate to the caller when that item is ready. The result of an order might look like this:

```shell
Received Neon Green Paint, time to mix it!
Received Horsehair brush, time to start painting!
```

### The Problem

Because the delivery time of our items is not guaranteed the output may be confusing.

```shell
Received Horsehair brush, time to start painting!
Received Neon Green Paint, time to mix it!
```

Do you see it? We got the brush first and immediately began painting, but how can we paint if we haven't received that item yet? Your goal for this challenge is to control the flow of information. No matter which order is prepared first we should always see the messages as such:

```shell
Received Neon Green Paint, time to mix it!
Received Horsehair brush, time to start painting!
```

While working on this problem you should also consider if your solution allows for additional orders. If I add a tarp to this equation where does it fit in the sequence and how well does your solution accomodate change, or simply, does it scale?  

Don't spend more than 30 minutes on this challenge. (cue Jeopardy! theme music)

---

## Understanding Promises

Now that you have a good understanding of handling asynchronous code with callbacks let's see some Promise syntax and determine how they can assist in writing cleaner code.  

```javascript
const promise = new Promise((resolve, reject) => {
});
```

A Promise is a constructor function that needs a callback (an executor) that accepts `resolve` and `reject`, both of which are functions. Any asyncronous operations you need to run will go inside the callback you pass to the promise. If whatever action our code is performing is successful you can notify the promise and pass data to it by calling `resolve(data)`. If there is an error or failure invoke `reject(error)` to tell the promise somethingwent wrong.  

```javascript
const promise = new Promise((resolve, reject) => {
});

console.log(promise);
// => Promise { <pending> }
```

A promise has three states: `pending`, `fulfilled` and `rejected`.  Pending is our current state and it will eventually transition into one of the other states. It simply means that the promise has not resolved or reject yet. A fulfilled state means that resolve has been called and the promise is complete. Rejected indicates the promise operation failed.  

## Handling Data

Knowing this we can start using promises to order our supplies.

```javascript
const paint = new Promise((resolve, reject) => {
  orderSupplies('paint', item => {
    resolve(item);
  });
});
```

This works, but our callback doesn't do anything other than pass the item to resolve. Since we don't have a failure case at this point we can pass resolve directly.  

```javascript
const paint = new Promise((resolve, reject) => {
  orderSupplies('paint', resolve);
});
```

Much better. How do we get our data from the promise? Other than initialization a promise will haves two methods to interact with and handle responses: `then` and `catch`. When the promise is successful, or resolves, it will call `then`, its success handler. If there is a problem and it rejects, it will call `catch`.  

```javascript
const paint = new Promise((resolve, reject) => {
  orderSupplies('paint', resolve);
});

paint
  .then(item => {
    // handle item
    receivedItem(item);
  })
  .catch(error => {
    // handle error
    console.log(error.message);
  });

// => Received Neon Green Paint, time to mix it!
```

Let's order both items with promises.  

```javascript
const paint = new Promise((resolve, reject) => {
  orderSupplies('paint', resolve);
});
const brush = new Promise((resolve, reject) => {
  orderSupplies('brush', resolve);
});

paint
  .then(item => {
    // handle item
    receivedItem(item);
  })
  .catch(error => {
    // handle error
    console.log(error.message);
  });

brush
  .then(item => {
    // handle item
    receivedItem(item);
  })
  .catch(error => {
    // handle error
    console.log(error.message);
  });
```

This may not produce the results we want though. Whatever item is ready first still gets printed first. So how has switching to promises helped us? The benefit is how we call on our data. Both items are ordered at relatively the same time, but that doesn't mean we have to handle that content as soon as it's ready. We can nest data access via then inside of another then.

```javascript
paint
  .then(item => {
    // handle item
    receivedItem(item);

    brush
      .then(item => {
        // handle item
        receivedItem(item);
      })
      .catch(error => {
        // handle error
        console.log(error.message);
      });
  })
  .catch(error => {
    // handle error
    console.log(error.message);
  });
```

This does not affect how we order items, only when we handle the response. It results in always handling brush after we receive paint, even when brush is prepared first.  

It's often the case that we handle errors for a similar set of promises in exactly the same way. Let's reuse paints catch with our brush promise. Promises can chain off themselves or other promises. All we need to do is return a promise from within a handler.  

```javascript
paint
  .then(item => {
    // handle item
    receivedItem(item);

    return brush
      .then(item => {
        // handle item
        receivedItem(item);
      });
  })
  .catch(error => {
    // handle error
    console.log(error.message);
  });
```

Returning brush after handling paint allows catch to be shared. If we wanted to clean up paints handler further we can move brushes `then` into the chain.  

```javascript
paint
  .then(item => {
    // handle item(paint)
    receivedItem(item);

    return brush;
  })
  .then(item => {
    // handle item(brush)
    receivedItem(item);
  });
  .catch(error => {
    // handle error
    console.log(error.message);
  });
```

In fact, we can move brush entirely out of the paint handler.  

```javascript
paint
  .then(item => {
    // handle item
    receivedItem(item);
  })
  .then(() => brush)
  .then(item => {
    // handle item
    receivedItem(item);
  });
  .catch(error => {
    // handle error
    console.log(error.message);
  });
```

While we are not accepting any data when returning brush into the chain, what information is received? Does it get the paint object again? `.then((paintItem???) => brush)`  

It does not. Once a promise has been handled (resolved or rejected) any subsequent then calls will receive undefined. That is until a new promise with new data is returned to the chain. It's also important to note that any `then` handler is called asynchronously, no matter if data is available or the operation within is synchronous.  

Let's clean this up a bit more then think about adding a tarp.  

```javascript
paint
  .then(receivedItem)
  .then(() => brush)
  .then(receivedItem)
  .catch(error => console.log(error.message));
```

Now it should be easy to add any new requirements. Add a tarp to the warehouse:

```javascript
warehouse = {
  paint: {
    product: 'Neon Green Paint',
    directions: () => 'mix it!'
  },
  brush: {
    product: 'Horsehair brush',
    directions: () => 'start painting!'
  },
  tarp: {
    product: 'A large tarp',
    directions: () => 'cover the floor!',
  }
};
```

and add it into the chain.

```javascript
const tarp = new Promise((resolve, reject) => {
  orderSupplies('tarp', resolve);
});

tarp
  .then(receivedItem)
  .then(() => paint)
  .then(receivedItem)
  .then(() => brush)
  .then(receivedItem)
  .catch(error => console.log(error.message));
```

## Order Supplies

Adding tarp was easy, but now we begin to see this is not sustainable. While our code is concise we have unnecessary duplication. Before we address this, let's quickly refactor order supplies to utilize promises rather than callbacks.  

Remove the callback from orderSupplies function definition and wrap any asynchronous code in a promise executor. We still don't have a failure scenario, so directly resolve the warehouse item. Don't forget to return said promise.  

```javascript
function orderSupplies(item) {
  let warehouse; //undefined
  const deliveryTime = Math.random() * 3000;

  return new Promise((resolve, reject) => {
    setTimeout(() => {
      warehouse = {
        paint: {
          product: 'Neon Green Paint',
          directions: () => 'mix it!'
        },
        brush: {
          product: 'Horsehair brush',
          directions: () => 'start painting!'
        },
        tarp: {
          product: 'A large tarp',
          directions: () => 'cover the floor!',
        }
      };

      resolve(warehouse[item]);
    }, deliveryTime);
  });
}
```

Now order tarp, paint and brush, handling them in sequence.  

```javascript
const tarp = orderSupplies('tarp');
const paint = orderSupplies('paint');
const brush = orderSupplies('brush');

tarp
  .then(receivedItem)
  .then(() => paint)
  .then(receivedItem)
  .then(() => brush)
  .then(receivedItem)
  .catch(error => console.log(error.message));
```

Great. Now we're back to where we were before the refactor with the benefit of a simpler public interface. How do we remove the need to call `receivedItem` for every warehouse item? We can ask the Promise constructor to handle any number of promises with a static `all` method. While not a requirement, it is a good idea to group promises that resolve similar data.  

```javascript
Promise.all([tarp, paint, brush])
  .then(items => items.forEach(receivedItem))
  .catch(error => console.log(error.message));
```

If content is order dependent, such as ours, be sure to list them in the desired position. The promise from the passed array will be mapped to the resolved data.  

## Error handling

So far we haven't had an error case. Using our current example where might we introduce an error? How about if we order something from our warehouse that it doesn't have?  

```javascript
return new Promise((resolve, reject) => {
  setTimeout(() => {
    warehouse = {
      paint: {
        product: 'Neon Green Paint',
        directions: () => 'mix it!'
      },
      brush: {
        product: 'Horsehair brush',
        directions: () => 'start painting!'
      },
      tarp: {
        product: 'A large tarp',
        directions: () => 'cover the floor!',
      }
    };

    if (item in warehouse) {
      resolve(warehouse[item]);
    } else {
      reject(new Error(`Item '${item}' is out of stock!`));
    }
  }, deliveryTime);
```

Ordering an out of stock item, such as a roller, will reject the promise, passing a error object with a descriptive message.  

```javascript
const roller = orderSupplies('roller');

roller
  .then(receivedItem)
  .catch(error => console.log(error.message));

// => Item 'roller' is out of stock!
```

What happens if we add the roller promise to `Promise.all`?  

```javascript
Promise.all([tarp, paint, brush, roller])
  .then(items => items.forEach(receivedItem))
  .catch(error => console.log(error.message));

// => Item 'roller' is out of stock!
```

The only output will be the error message from the roller rejection. When using `all` if *any* of the passed promises is rejected the entire operation fails.  

Would it be better to go back to using our multiple promise chains?  

```javascript
tarp
  .then(receivedItem)
  .then(() => paint)
  .then(receivedItem)
  .then(() => brush)
  .then(receivedItem)
  .then(() => roller)
  .then(receivedItem)
  .catch(error => console.log(error.message));
```

This could work and you will get the desired ordered output. Assuming a Node environment eventually you will get these messages:  

* `UnhandledPromiseRejectionWarning: Error: Item 'roller' is out of stock!`
* `UnhandledPromiseRejectionWarning: Unhandled promise rejection`
* `PromiseRejectionHandledWarning: Promise rejection was handled asynchronously (rejection id: 1)`

Initially Node thinks we failed to handle our rollers rejection. When we finally handle it, after receiving and printing all other orders, Node issues another warning about handling failures asynchronously. You need to handle rejects synchronously. If you must handle later there are npm modules available to facilitate or you can roll your own solution.  

Add a catch to our roller order, and refaactor our error handler.  

```javascript
const handleError = error => console.log(error.message);

const roller = orderSupplies('roller').catch(handleError);
```

Example output:

```shell
Received A large tarp, time to cover the floor!
Item 'roller' is out of stock!
Received Neon Green Paint, time to mix it!
Received Horsehair brush, time to start painting!
Cannot read property 'product' of undefined
```

We got rid of Nodes error messages but now we have a new one. Because we handled rollers failure with catch, when the roller promise is returned to the chain it has no data to resolve, so undefined is passed to receivedItem. You can easily fix this by adding a check in receivedItem.  

For now remove roller from `all` and that will be our final solution.  

## Conclusion

Promises assist in handling asynchronous code using a simple interface with predictable outcomes.  