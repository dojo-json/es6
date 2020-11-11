# for-in

If you're familiar with Python and started learning JavaScript, one of the most common problems is misuse of the `for in` loop. Pythons for-in operates on list elements

```python
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
  print(fruit)

# apple
# banana
# cherry
```

and coming to a JavaScript environment, you expect the same but are confused by the unexpected output.

```javascript
var fruits = ["apple", "banana", "cherry"];
for (var fruit in fruits) {
  console.log(fruit);
}

// 0
// 1
// 2
```

The reason, of course, is that JS for-in is meant to iterate over objects keys, not array elements. While for-in remains the same in ES6, a new type of `for` loop has been introduced for just this purpose.  

## for-of

`for of` works on individual array elements.

```javascript
const fruits = ["apple", "banana", "cherry"];
for (const fruit of fruits) {
  console.log(fruit);
}

// apple
// banana
// cherry
```

Excellent! But there will still be times that you want to know the index of the current element. How do we get that?

By itself, you can't access index with a for-of loop, but array functionality has been extended with an `entries` method. Let's see what that does.

```javascript
const fruits = ["apple", "banana", "cherry"];

console.log(fruits.entries());

// => Object [Array Iterator] {}
```

Interesting, what's an iterator? According to MDN `an iterator is an object which defines a sequence and potentially a return value upon its termination.` If that didn't clear things up, no worries, we don't need to know the ins and outs of iterators. For our purposes just know that we can iterate, or loop, through this object.

```javascript
const fruits = ["apple", "banana", "cherry"];
for (const fruit of fruits.entries()) {
  console.log(fruit);
}

// [ 0, 'apple' ]
// [ 1, 'banana' ]
// [ 2, 'cherry' ]
```

When iterating over entries, for each element, we receive an array of the index and the element at that position. If we want to now use this content we could do this:

```javascript
for (const fruit of fruits.entries()) {
  console.log(`${fruit[1]} was found at ${fruit[0]}`);
}

// apple was found at 0
// banana was found at 1
// cherry was found at 2
```

We could reliably depend on `fruit[1]` to give us the element and `fruit[0]` to access the index, but we have much more verbose tools at our disposal. Using array destructuring we learned about previously will offer better context and readability.

```javascript
for (const [index, fruit] of fruits.entries()) {
  console.log(`${fruit} was found at ${index}`);
}
```

Same output, so much easier to reason. Can I use for-of on objects?

```javascript
const person = {
  firstName: 'Bob',
  lastName: 'Marley',
  email: 'bob@marley.com',
  username: 'barley',
};

for (const property of person) {
  console.log(property);
}
```

Not unexpectedly this produces an error: `TypeError: person is not iterable`. That doesn't mean we can't use `for-of` on objects, we just need something to make that object iterable. `Object.entries` works similarly to our array entries method, giving us an iterable collection of arrays of key-value pairs.

```javascript
Object.entries(person);
// [
//   [ 'firstName', 'Bob' ],
//   [ 'lastName', 'Marley' ],
//   [ 'email', 'bob@marley.com' ],
//   [ 'username', 'barley' ]
// ]
```

Perfect. Utilizing entries in our loop, and destructuring makes viewing key-value pairs a simple task.

```javascript
const person = {
  firstName: 'Bob',
  lastName: 'Marley',
  email: 'bob@marley.com',
  username: 'barley',
};

for (const [key, value] of Object.entries(person)) {
  console.log(`${key} has the value ${value}`);
}
```

The for-of loop has simplified array element interactions, and coupled with entries and destructuring, makes working with more complex structures streamlined.