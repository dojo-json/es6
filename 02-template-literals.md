# Template Literals

Have you ever built a multiline string in JavaScript by seemingly endless concatenation?

```javascript
var paragraph = 'Lorem ipsum dolor sit amet, consectetur adipiscing elit, \n' +
  'sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. \n' + 
  'At tellus at urna condimentum mattis pellentesque. Suscipit tellus \n' + 
  'mauris a diam maecenas sed enim ut sem. Consectetur a erat nam at \n' + 
  'lectus. A iaculis at erat pellentesque adipiscing commodo elit. Elit \n' +
  'sed vulputate mi sit. Bibendum at varius vel pharetra vel. Id \n' + 
  'consectetur purus ut faucibus pulvinar elementum integer enim. Nunc \n' +
  'sed id semper risus in hendrerit. Quam pellentesque nec nam aliquam. \n' + 
  'Sapien faucibus et molestie ac feugiat sed. Libero justo laoreet sit \n' + 
  'amet cursus sit amet dictum sit. Eget nullam non nisi est sit amet \n' + 
  'facilisis magna. Lacus vel facilisis volutpat est velit egestas dui \n' +
  'id. Varius morbi enim nunc faucibus a pellentesque. Tempor nec \n' + 
  'feugiat nisl pretium fusce id. Elit sed vulputate mi sit amet mauris \n' + 
  'commodo quis. Sit amet dictum sit amet. Fusce id velit ut tortor \n' + 
  'pretium viverra suspendisse. Nam libero justo laoreet sit amet cursus sit.';
```

It's slow, it's painful. Start throwing in variable concatenation and it becomes a recipe for frustration. Luckily ES6 has introduced Template Literals (or template strings) that aim to assuage your suffering.  

Instead of using quotations to block your code we shall encapsulate strings using the grave accent, or `backtick` (`).

Rewriting a portion of the above example looks like this:

```javascript
const paragraph = `Lorem ipsum dolor sit amet, consectetur adipiscing elit,
  sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.
  At tellus at urna condimentum mattis pellentesque. Suscipit tellus
  mauris a diam maecenas sed enim ut sem. Consectetur a erat nam at
  lectus. A iaculis at erat pellentesque adipiscing commodo elit. Elit
  sed vulputate mi sit. Bibendum at varius vel pharetra vel. Id
  consectetur purus ut faucibus pulvinar elementum integer enim. Nunc
  sed id semper risus in hendrerit. Quam pellentesque nec nam aliquam.`;
```

Newlines, tabs and spacing will all be respected when using template strings taking much of the guesswork out of formatting your content.

Utilizing variables in your strings has become significantly easier with expression interpolation.

A simple example:

```javascript
var name = 'Bob';
var hello = 'Hello ' + name;
```

becomes

```javascript
const name = 'Bob';
const hello = `Hello ${name}`;
```

The `${}` syntax evaluates the enclosed expression, in this case a simple string variable, and places the results in that position. You are not limited to variables, you may perform any valid JavaScript expression, including function invocations using interpolation syntax.  

Building more complex formatted material is significantly easier.  

```javascript
function buildCardFor(person) {
  return (
    "<div class='card'>" +
      "<h3 class='float-right'>" +
      "<small class='text-uppercase'>Joined " + new Date(person.createdAt) + "</small>" +
    "</h3>" +
    "<h2 class='h3'>" +
      person.firstName + " " + person.lastName + 
      "<small>" + person.username + "</small>" +
    "</h2>" +
    "<h4>" + person.email + "</h4>" +
  "</div>"
  );
}
```

transforms into..

```javascript
function buildCardFor(person) {
  return `
    <div class='card'>
      <h3 class='float-right'>
        <small class='text-uppercase'>Joined ${new Date(person.createdAt)}</small>
      </h3>
      <h2 class='h3'>
        ${person.firstName} ${person.lastName}
        <small> ${person.username} </small>
      </h2>
      <h4> ${person.email} </h4>
    </div>
  `;
}
```