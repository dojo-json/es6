# Rest/Spread 

ES6 provides us with a new operator, `...`, that is context dependent. Its purpose is to capture the `rest` of a data set, or to `spread` content of an existing structure.  

## Functions

Rest is often used with parameters and destructuring. Let's begin with writing a function that can accept any number of parameters.  

```javascript
function min() {
  var numbers = Array.prototype.slice.call(arguments);
  return Math.min.apply(null, numbers);
}

min(10, 92, 17, 31, 22);
```

ES6 rest allows a much simpler syntax to collect all arguments.  

```javascript
function min(...numbers) {
  return Math.min.apply(null, numbers);
}

min(10, 92, 17, 31, 22);
```

`...numbers` will pick up any passed content that does not have a named parameter. numbers will always be an array, even if the function is invoked without any parameters. Rest doesn't need to be the only parameter, but it does need to be the last.  

```javascript
function doStuff(param1, param2, ...extras) {
  ...
}
```

Using `...` as a spread operator can make our example even simpler.  

```javascript
function min(...numbers) {
  return Math.min(...numbers);
}

min(10, 92, 17, 31, 22);
```

Use of `...numbers` in this context will spread the elements to our call to Math.min. Assuming we collect numbers to then pass to min our example looks like this.

```javascript
const collect = [10, 92, 17, 31, 22];

// collect more numbers

function min(...numbers) {
  return Math.min(...numbers);
}

min(...collect);
```

## Destructuring

Now that we understand using rest and spread we can work it into our destructuring. Remembering our array example from the destructuring chapter we wanted to extract the first couple animals from our list.

```javascript
const animals = ['horse', 'dog', 'fish', 'cat', 'bird'];
const [firstAnimal, secondAnimal] = animals;
```

If we want the remaining animals, using a rest pattern at the end of the assignment.

```javascript
const [firstAnimal, secondAnimal, ...otherAnimals] = animals;
otherAnimals
// => ['fish', 'cat', 'bird']
```

## Objects

Pretty neat. Can we do the same with objects? Why yes we can. While object spread came about a few years after ES2015 it seems like a good idea to examine it here.  

```javascript
const person = {
  firstName: 'Bob',
  lastName: 'Marley',
  email: 'bob@marley.com',
  password: 'sekureP@ssw0rd9',
  username: 'barley',
  addresses: [
    {
      address: '1600 Pennsylvania Avenue',
      city: 'Washington, D.C.',
      zipcode: '20500',
    },
    {
      address: '221B Baker St.',
      city: 'London',
      zipcode: 'WC2N 5DU',
    }
  ],
  createdAt: 1543945177623
};
```

Grabbing `firstName` and `lastName` from person is easy, along with assigning all other properties to a 'catchall'.  

```javascript
const { firstName, lastName, ...attributes } = person;
```

Using spread we can quickly make complete copies of objects or arrays.  

```javascript
const personCopy = { ...person };
```

There are some limitations. The copy is shallow, so any complex or nested structures with objects references will still point to the same object. We'll use our person objects to demonstrate.  

```javascript
const personCopy = { ...person };

personCopy === person
// => false

personCopy.addresses === person.addresses
// => true
```
