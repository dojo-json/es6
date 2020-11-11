# Class Syntax

ES6 introduces new syntax for creating and extending JavaScript objects using the `class` and `extends` keywords. In this section we will refactor traditional constructor functions using the updated syntax.  

## Prototype

For a quick recap on building constructor functions and using `prototype` let's take a look at this example:

```javascript
function Deck() {
  this.initialize();
}

Deck.prototype.initialize = function() {
  var suits = ['Diamond', 'Heart', 'Spade', 'Club'];
  var faces = ['Ace', '2', '3', '4', '5', '6', '7', '8', '9', '10', 'Jack', 'Queen', 'King'];
  var deck = [];

  for (var sindex = 0; sindex < suits.length; sindex++) {
    for (var findex = 0; index < faces.length; findex++) {
      deck.push(this.createCard(suits[sindex], faces[findex]));
    }
  }

  this.deck = deck;
};

Deck.prototype.createCard = function(suit, face) {
  return new Card(suit, face);
};

function Card(suit, face) {
  this.suit = suit;
  this.face = face;
}

Object.defineProperties(Card.prototype, {
  image: {
    get: function () {
      return this.suit.toLowerCase() + '-' + this.face.toLowerCase() + '.jpg';
    }
  },
  value: {
    get: function () {
      switch (this.face.toLowerCase()) {
        case 'ace':
          return 14;
        case 'king':
          return 13;
        case 'queen':
          return 12;
        case 'jack':
          return 11;
        default:
          return parseInt(this.face, 10);
      }
    }
  }
});
```

For our `Deck` we have a constructor and a couple prototype methods, `initialize` to create a deck and `createCard` to isolate card creation. `Card` has no methods but we define properties to fetch an image name and a card value.  

We'll begin converting this to ES6 `class` based syntax, starting with our Deck.  
Use the keyword `class` followed by the name of your class, what used to be your constructor function name, and curly braces for your class body.

```javascript
class Deck {

}
```

Simple. You have a very basic Deck class which you can begin using immediately: `const deck = new Deck();` Defining our methods is just as easy.

```javascript
class Deck {
  initialize() {
    ...
  }

  createCard(suit, face) {
    return new Card(suit, face);
  }
}
```

Any methods defined within the `class` body are automatically added to its `prototype`. The class syntax in ES6 is often referred to as `syntactic sugar`. In other words, it is just a fancy wrapper for creating traditional prototypal structures in JavaScript.  

In our ES5 example, upon Deck invocation `initialize` is immediately called.  With our new class we can mirror that by defining a `constructor`.

```javascript
class Deck {
  constructor() {
    this.initialize();
  }

  ...
}
```

Your constructor, just like any JS function, can accept any number of arguments and assign them to instance variables. Let's see what that looks like with our `Card` class.  

```javascript
class Card {
  constructor(suit, face) {
    this.suit = suit;
    this.face = face;
  }
}
```

No surprises here. Setting instance variables is the same as it's always been. Let's complete our refactor by adding properties to Card. We no longer need to use the `Object` constructor to define these properties. Simply add getter and setter functions to our class structure, preceded by either `get` or `set`.

```javascript
class Card {
  constructor(suit, face) {
    this.suit = suit;
    this.face = face;
  }

  get image() {
    return `${this.suit.toLowerCase()}-${this.face.toLowerCase()}.jpg`;
  }

  get value() {
    switch (this.face.toLowerCase()) {
      case 'ace':
        return 14;
      case 'king':
        return 13;
      case 'queen':
        return 12;
      case 'jack':
        return 11;
      default:
        return parseInt(this.face, 10);
    }
  }
}
```

We aren't `set`ting anything yet, but the syntax is similar. Assume we want to protect setting a value on card.  

```javascript
class Card {
  set value(someValue) {
    if (typeof someValue !== 'number') {
      throw new Error(`'value' must be numeric!`);
    }

    this._value = someValue;
  }

  get value() {
    return this._value;
  }
}

card.value = 'not numeric';
// => error
```

## Inheritance

With our refactor complete we will be examining how to extend object functionality using the `extends` keyword. To demonstrate this we'll further refactor the `Card` class using polymorphism. First a quick example of Prototypal inheritance.

```javascript
function Card(suit, face) {
  this.suit = suit;
  this.face = face;
}

function CardKing(suit, face) {
  Card.call(this, suit, face);
}

CardKing.prototype = Object.create(Card.prototype);
CardKing.prototype.constructor = CardKing;
```

Our new CardKing class calls Card to inherit instance variables. We directly assign Card prototype to CardKing prototype to inherit the prototype chain. Doing this has the effect of overwriting CardKing constructor with our base Card constructor, so we need to reassign CardKing function as the constructor.

Whew, prototypal inheritance is involved. Let's see how we can simplify this with `extends`.

```javascript
class Card {
  constructor(suit, face) {
    this.suit = suit;
    this.face =face;
  }
}

class CardKing extends Card {
  constructor(suit, face) {
    super(suit, face);
  }
}
```

`CardKing` extends `Card` in the class definition. The constructor accepts our arguments and using the `super` keyword, calls the parent class passing in the requisite data. That's it!

We're going to slightly alter Cards interface and quickly create the rest of our extended Card classes.

```javascript
class Card {
  constructor(suit, face, value = face) {
    this.suit = suit;
    this.face = face;
    this.value = value;
  }
}

class CardAce extends Card {
  constructor(suit, face) {
    super(suit, face, 14);
  }
}

class CardKing extends Card {
  constructor(suit, face) {
    super(suit, face, 13);
  }
}

class CardQueen extends Card {
  constructor(suit, face) {
    super(suit, face, 12);
  }
}

class CardJack extends Card {
  constructor(suit, face) {
    super(suit, face, 11);
  }
}
```

## Static

To fully take advantage of our Polymorphic classes we need to define a new way to create cards. We obviously don't want the card instances, or any other object determining card class, so we should add a static method that performs this task. To create static methods or properties simply prepend the `static` keyword to a method or property definition within the class.  

```javascript
class Card {
  constructor(suit, face, value = face) {
    this.suit = suit;
    this.face = face;
    this.value = value;
  }

  static for(suit, face) {
    switch (face.toLowerCase()) {
      case 'ace':
        return new CardAce(suit, face);
      case 'king':
        return new CardKing(suit, face);
      case 'queen':
        return new CardQueen(suit, face);
      case 'jack':
        return new CardJack(suit, face);
      default:
        return new Card(suit, face, parseInt(face, 10));
    }
  }
}
```

Now any callers of Card can use the `for` method to create a card object.  

```javascript
class Deck {
  ...

  createCard(suit, face) {
    return Card.for(suit, face);
  }
}
```

Nice. Creating cards has never been easier. One last refactor to simplify our for method.

```javascript
class Card {
  ...

  static for(suit, face) {
    try {
      return new Cards[face.toLowerCase()](suit, face);
    } catch {
      return new Card(suit, face, parseInt(face, 10));
    }
  }
}

const Cards = {
  ace: CardAce,
  king: CardKing,
  queen: CardQueen,
  jack: CardJack,
};
```

## new

In addition to the updated class syntax, ES6 has extended `new`, making it easier to determine how a function was called.  

```javascript
function Deck() {
  if (!new.target) {
    return new Deck();
  }
}

const deck = Deck();
```

It's important to note that when using `class` syntax you *must* use new when invoking the constructor.

```javascript
class Deck {

}

const deck = Deck();
```

The above will result is the following error: `TypeError: Class constructor Deck cannot be invoked without 'new'`.  

One last refactor to our example. Let's make `Card` an abstract class using new to determine type.  

```javascript
class Card {
  constructor(suit, face, value) {
    if (new.target === Card) {
      throw new Error('Card is an abstract and may not be invoked directly');
    }

    ...
  }
}

const card = new Card('Diamond', '10', 10);
// => error
```

Now calling card directly will result in an error. You should create a new polymorphic class for numeric values and apply that change to Cards static for method.

## Conclusion

ES6 updated our ability to organize classes into well defined structures while simplifying inheritance and function invocation target determination. You should continue to add features to the examples used in this section such as shuffling the deck, drawing a card and perhaps some player classes to interact with.