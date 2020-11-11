# Objects

ES6 has introduced enhanced means of interacting with object literals.

Consider the usual method of adding properties to an object when defined.

```javascript
var name = 'Forrest';
var height = '5ft 8in';
var email = 'forrest@gump.com';

var forrest = {
  name: name,
  email: email,
  height: height,
};
```

You have variables containing various data and assign those variables to object properties, often using the same name. This repetition is no longer necessary in ES6.

```javascript
const name = 'Forrest';
const height = '5ft 8in';
const email = 'forrest@gump.com';

const forrest = {
  name,
  email,
  height,
};
```

This new syntax, while being more succinct, creates the extact same object as the previous example and it's not limited to data. You may add functions in the same manner.

```javascript
function sayHello(name) {
  console.log(`Hello ${name}!`);
}

const forrest = {
  name,
  email,
  height,
  sayHello,
};
```

It doesn't need to be a standalone function, you can take advantage when creating object methods.

```javascript
const forrest = {
  name,
  email,
  height,
  sayHello(name) {
    console.log(`Hello ${name}`);
  },
};
```

Note the omission of the `function` keyword.  

## Dynamic Properties

Often you want to create objects that don't have predefined properties. Traditionally you would do this:

```javascript
var skill = 'run';
var skillDescription = 'I like running';
var name = 'forrest';
var height = '5ft 8in';
var email = 'forrest@gump.com';

var forrest = {
  name: name,
  email: email,
  height: height,
};

forrest[skill] = skillDescription;
```

Of course, now there is a better way.

```javascript
const skill = 'run';
const skillDescription = 'I like running';
const name = 'forrest';
const height = '5ft 8in';
const email = 'forrest@gump.com';

const forrest = {
  name: name,
  email: email,
  height: height,
  [skill]: skillDescription,
};
```

Enclosing your dynamic variable within square brackets in your object definition will result in its value being the property name of the corresponding value.

```javascript
{
  ...
  run: 'I like running'
}
```

Attempting this syntax before ES6 would result in an error: `SyntaxError: Unexpected token [`  

Pretty sweet. Let's do a quick exercise using dynamic properties to build nested objects. First, how might this look pre-ES6?

```javascript
var keys = ['level1', 'level2', 'level3', 'level4', 'level5'];
var value = 'content to insert';

function nest(array, insert) {
  var obj = insert;
  
  for (var index = array.length -1; index >= 0; index--) {
    obj = {};
    obj[array[index]] = insert;
    insert = obj;
  }
  
  return obj;
}

var nested = nest(keys, insert);

console.log(nested);

/*
  {
    level1: {
      level2: {
        level3: {
          level4: {
            level5: 'content to insert'
          }
        }
      }
    }
  }
*/
```

Pretty straight-forward; we declare an object and loop through the passed array. For every iteration of the loop we reinitialize our object and, using index, assign the current element as the property with the previous content as its value.  

Lets refactor this code using ES6 capabilities and a more functional approach.  

```javascript
const keys = ['level1', 'level2', 'level3', 'level4', 'level5'];
const value = 'content to insert';

function nest(array, insert) {
  let obj = insert;
  array.reverse().forEach(key => {
    obj = { [key]: insert };
    insert = obj;
  });

  return obj;
}

const nested = nest(keys, insert);
```

Using `forEach` helps move us in the functional direction. Now when we reinitialize our object we can set the dynamic property on creation. Pretty good, but we can do better.  

```javascript
function nest(array, insert) {
  return array.reduceRight(function(accumulator, key) {
    return {
      [key]: accumulator
    }
  }, insert);
}
```

Clean and concise, which is exactly what Enhanced Object Literals are all about.