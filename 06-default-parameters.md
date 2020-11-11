# Default Parameters

A much called for JavaScript feature, default parameters, is finally available in ES6.  

How it used to work.

```javascript
function inRange(number, min, max) {
  min = min || 0;
  max = max || 1;

  return Math.min(min, max) <= number && number < Math.min(min, max);
}
```

With default parameters our code becomes just a little easier.  

```javascript
function inRange(number, min = 0, max = 1) {
  return Math.min(min, max) <= number && number < Math.min(min, max);
}
```

Value assignments directly in our parameters list provide defaults if not enough arguments are passed.  

## Destructuring

Defaults are simple. We can make them even more useful with destructuring.  

```javascript
const { eyeColor = 'blue' } = person;
// => blue

const [firstAnimal = 'koala'] = [];
// => koala
```

We can even destructure and assign default values to objects passed to functions.  

```javascript
const credentials = {
  email: 'bob@marley.com',
  password: 'sekureP@ssw0rd9',
  username: 'barley',
};


function login({ email = 'some@email.com', password = 'superPass', username = 'noname' }) {
 ...
}

login(credentials);
```

Assuming you wanted to use all the defaults for a function you might think you can simply invoke and pass no arguments. But doing so will result in an error: `TypeError: Cannot destructure property 'email' of 'undefined' or 'null'.` You can achieve your desired result in one of two ways.  

```javascript
login({});
```

Pass an empty object, or supply your destructuring object with an empty default object.  


```javascript
function login({ email = 'some@email.com', password = 'superPass', username = 'noname' } = {}) {
 ...
}

login();
```

Now the default object, which has no properties, is supplied to the destructuring assignment, which then uses the default values for email, password and username.  Pretty useful.
