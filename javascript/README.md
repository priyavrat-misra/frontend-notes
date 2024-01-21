# JavaScript

### Table of Contents
- [The Execution Context - Creation & Hoisting](#the-execution-context---creation--hoisting)
- [Conceptual Aside - Single Threaded, Synchronous Execution](#conceptual-aside---single-threaded-synchronous-execution)
- [Function Invocation and the Execution Stack](#function-invocation-and-the-execution-stack)
- [Functions, Context, and Variable Environments](#functions-context-and-variable-environments)
- [The Scope Chain](#the-scope-chain)
- [Scope, ES6, and `let`](#scope-es6-and-let)
- [What About Asynchronous Callbacks](#what-about-asynchronous-callbacks)
- [Conceptual Aside Types And Operators](#conceptual-aside-types-and-operators)
- [Primitive Types](#primitive-types)
- [Conceptual Aside - Operators](#conceptual-aside---operators)
- [Operator Precedence and Associativity](#operator-precedence-and-associativity)
- [Conceptual Aside Coercion](#conceptual-aside-coercion)
- [Comparison Operators](#comparison-operators)
- [Existence and Booleans](#existence-and-booleans)
- [Default Values](#default-values)
- [Framework Aside: Default Values](#framework-aside-default-values)
- [Objects and the Dot](#objects-and-the-dot)
- [Objects and Object Literals](#objects-and-object-literals)
- [Framework Aside: Faking Namespaces](#framework-aside-faking-namespaces)
- [JSON and Object Literals](#json-and-object-literals)
- [Functions are Objects](#functions-are-objects)
- [Function Statements and Function Expressions](#function-statements-and-function-expressions)
- [Conceptual Aside By Value vs By Reference](#conceptual-aside-by-value-vs-by-reference)
- [Objects, Functions, and `this`](#objects-functions-and-this)
- [Conceptual Aside - Arrays  - Collections of Anything](#conceptual-aside---arrays----collections-of-anything)
- [`arguments` and spread](#arguments-and-spread)
- [Dangerous Aside Automatic Semicolon Insertion](#dangerous-aside-automatic-semicolon-insertion)
- [Immediately Invoked Functions Expressions (IIFEs)](#immediately-invoked-functions-expressions-iifes)
- [Framework Aside IIFEs and Safe Code](#framework-aside-iifes-and-safe-code)
- [Understanding Closures](#understanding-closures)
- [Closures And Callbacks](#closures-and-callbacks)
- [`call()`, `apply()`, and `bind()`](#call-apply-and-bind)
- [Functional Programming](#functional-programming)
- [Understanding The Prototype](#understanding-the-prototype)
- [Everything is an Object (Or a primitive)](#everything-is-an-object-or-a-primitive)
- [Reflection and Extend](#reflection-and-extend)
- [Functions Contructors, `new` And The History Of JavaScript](#functions-contructors-new-and-the-history-of-javascript)
- [Function Constructors and `.prototype`](#function-constructors-and-prototype)
- [Dangerous Aside `new` and functions](#dangerous-aside-new-and-functions)
- [Conceptual Aside Built-In Function Constructors](#conceptual-aside-built-in-function-constructors)
- [Dangerous Aside Built-In Function Constructors 2](#dangerous-aside-built-in-function-constructors-2)
- [Dangerous Aside Arrays and `for`..`in`](#dangerous-aside-arrays-and-forin)
- [`Object.create` and Pure Prototypal Inheritance](#objectcreate-and-pure-prototypal-inheritance)
- [ES6 and Classes](#es6-and-classes)
- [Initialization](#initialization)
- [`typeof`, `instanceof`, and Figuring Out What Something Is](#typeof-instanceof-and-figuring-out-what-something-is)
- [Strict Mode](#strict-mode)
- [Learning From Other's Good Code](#learning-from-others-good-code)
- [Deep Dive into Source Code jQuery - Part 1](#deep-dive-into-source-code-jquery---part-1)
- [Deep Dive into Source Code jQuery - Part 2](#deep-dive-into-source-code-jquery---part-2)
- [Deep Dive into Source Code jQuery - Part 3](#deep-dive-into-source-code-jquery---part-3)
- [Requirements](#requirements)
- [TypeScript, ES6, and Transpiled Languages](#typescript-es6-and-transpiled-languages)


## The Execution Context - Creation & Hoisting
The execution context is created in two phases:
1. **creation phase**: the _global object_, a special variable (`this`), and a reference to the _outer environment_ (if there is one) is setup in memory. And memory space for variables and functions is reserved (_"hoisting"_). The functions in its entirety are placed into memory space. However, for variables JS engine puts a placeholder `undefined`, because it doesn't know what it's value will ultimately end up being until it starts executing that line of code.
2. **execution phase**: here the code is exectued line by line and assignment statements are executed.

```javascript
console.log(a);  // undefined
b();  // 'This works!'

var a = 'Hello World!';
function b() {
	console.log('This works!');
}
```

When parser runs through code it recognizes where we created variables and functions and it sets up memory space for them. So before our code begins to be executed, JS engine has already set aside memory space for all the variables and functions we created. When the code begins to execute line by line it can access it.

## Conceptual Aside - Single Threaded, Synchronous Execution
**Single threaded** - JavaScript is a single threaded, synchronous language. That means one command execution at a time. Maybe not under the hood of the browser but from our perspective as programmers it is single threaded.

**Synchronous execution** - means one at a time and in order that it appears. 

## Function Invocation and the Execution Stack
**Invocation** - running or calling a function by using parenthesis `()`.

**Execution Stack** - every time we invoke a function a new execution context is created for that function and is put on top of execution stack.

## Functions, Context, and Variable Environments
**Variable environments** - where the variables live and how they relate to each other in memory. Every execution context has its own variable environment.

## The Scope Chain
If JavaScript engine doesn't find variable in it's own environment it looks in the outer environment. Where the outer environment points depends on where the function sits lexically. The chain of references to outer lexical environments is the **scope chain**.

So in this example `myVar1` would actualy log `1` even though it sits inside a function which is inside another. `myVar1` sits in the outer global environment so JS will go down the scope chain until it finds it.
```javascript
function a() {
    var myVar2 = 2;
    function b() {
        console.log(myVar1);  // 1
        console.log(myVar2);  // 2
    }
    b();
}

var myVar1 = 1;
var myVar2 = 1;
a();
b();  // Uncaught ReferenceError, as it is not present in this execution context
```

## Scope, ES6, and `let`
**Scope** - where a variable is available in our code. And if it's truly a new variable, or a new copy.

**`let`** - allows JS engine block scoping. During execution context that variable is still placed in memory and set to `undefined`, however, we are not allowed to use it until the line of code is run during the execution phase. So if we try to use a variable before, we will get an error. Also, it is scoped to the block. So if we are running `let` inside a loop a new variable will be placed in memory after each iteration.

```javascript
const fs = (function() {
    const arr = [];
    for (var i = 0; i < 3; ++i) {
        arr.push(function() {
            console.log(i);
        });
    }
    return arr;
})();

fs[0]();  // 3
fs[1]();  // 3
fs[2]();  // 3
```
```javascript
const fs = (function() {
    const arr = [];
    for (let i = 0; i < 3; ++i) {
        arr.push(function() {
            console.log(i);
        });
    }
    return arr;
})();

fs[0]();  // 0
fs[1]();  // 1
fs[2]();  // 2
```

## What About Asynchronous Callbacks
**Asynchronous** - executed more than one at a time. What is happening inside JS engine is synchronous. Its just the browser is putting things asynchronously in the event queue.

**Event queue** - events like click, HTTP event etc. are placed in the queue. JS starts processing this event queue only when the execution stack is empty.

```javascript
function waitThreeSeconds() {
    var ms = 3000 + new Date().getTime();
    while (new Date() < ms) {}
    console.log('finished function');
}

function clickHandler() {
    console.log('click event!');
}

document.addEventListener('click', clickHandler);
waitThreeSeconds();
console.log('finished execution');
```

## Conceptual Aside Types And Operators
**Dynamic typing** - we don't tell the engine what type of data a variable holds, it figures out while our code is running. Variable can hold different types of values because it's all figured out during execution.

## Primitive Types
**Primitive type** - a type of data that represents a single value. That is, not an object.
- **`undefined`** - represents lack of existence. We shouldn't set a variable to `undefined` ourself.
- **`null`** - also represents lack of existence. We can set a variable to `null`.
- **`boolean`** - `true` or `false`.
- **Number** - floating point number (there's always some decimals).
- **String** - a sequence of characters in 'single' or "double quotes".
- **Symbol** - used in ES6.

## Conceptual Aside - Operators
**Operator** - a special function that is written differently. Generally operators take two parameters and return a result.

## Operator Precedence and Associativity
**Operator precedence** - which operator function gets called first on the same line of code. Functions are called in order of precedence (higher precedence wins).

**Associativity** - what order operator functions get called in: left-to-right or right-to-left when they have the same precedence.

[Read more...](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Operator_Precedence)

## Conceptual Aside Coercion
**Coercion** - conversion of a value from one type to another. This happens quite often in JavaScript because it's dynamically typed. For example adding a number to a string would result in number being converted to a string:
```javascript
7 + '7'  // would be = '77'
 ```

## Comparison Operators
`===` is a strict comparison operator and doesn't do conversion. So it's the best practice to always use it to prevent strange bugs due to conversion. [Read more...](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Equality_comparisons_and_sameness)
```javascript
> '1' == 1  // true
> '1' === 1  // false
```
```javascript
> Number(true)  // 1
> Number(false)  // 0
> Number('')  // 0
> Number(null)  // 0
> Number(undefined)  // NaN
> Number('3')  // 3
```

## Existence and Booleans
`undefined`, `null` and `''` imply lack of existence and they convert to `false`.
```javascript
> Boolean(undefined)  // false
> Boolean(null)  // false
> Boolean('')  // false
```

We can use that to our advantage. This pattern is used in many JS libraries and good open source code. 
Whatever is in parenthesis of if statement is converted to a boolean. So this statement returns `true` if `a` is set or `false` otherwise.
```javascript
var a;
if (a) {
	console.log('Something is there.');
}
```

## Default Values
If we pass two values to `||` operator it will return the first one which returns `true`.

This pattern is used in many open source code:
```javascript
var name = name || "your name";
``` 
This way we can set a default parameter value in case no arguments are passed during invocation of a function.

## Framework Aside: Default Values
When using a few libraries to avoid overwriting variables, most libraries use this pattern:
```javascript
window.variableName = window.variableName || "String";
```

## Objects and the Dot
**Objects** are name value pairs that are sitting in memory and have references to other things inside them like properties (primitives or objects) and methods.

```javascript
var person = new Object();  // not a preferred way to create objects

person['firstName'] = 'John';

var lastNameProperty = 'lastName';
person[lastNameProperty] = 'Doe';

person.address = new Object();
person.address.street = "111 Main St.";
person.address.city = 'New York';
```
`[]` is called **computed member access operator** whereas, `.` is called **member access operator**. `[]` is usually helpful when we need to dynamically decide which property to get or set.

## Objects and Object Literals
Object literal syntax is more clean and preferred way to create objects.
```javascript
var person = {};
```
is same as `var person = new Object();` but we can do a lot more than that, we can initialize the object as such:
```javascript
var person = { 
    firstName: 'John', 
    lastName: 'Doe',
    address: {
        street: '111 Main St.',
        city: 'New York'
    }
};
person.email = 'john.doe@example.com';  // it is possible to add new properties later as well
```

We can create an object on the fly like any other variables:
```javascript
function greet(person) {
    console.log("Hi " + person.firstName + " " + person.lastName + "!");
}
greet({ firstName: 'Jane', lastName: 'Doe' });
```

## Framework Aside: Faking Namespaces
**Namespace** - a container for variables and functions. Typically to keep variables and functions with the same name separate.

We can prevent name collisions by using objects to store properties: 
```javascript
var english = {}; 
var spanish = {};
english.greet = 'Hello!';
spanish.greet = 'Hola!';
```

## JSON and Object Literals
**JSON** - _JavaScript Object Notation_ is inspired by JS object literal syntax. In JSON property names must be wrapped in double quotes.
- `JSON.stringify(objectLiteral)` - converts object to JSON.
- `JSON.parse(jsonString)` - converts `jsonString` to a JavaScript object.

## Functions are Objects
In JavaScript functions are objects.

**First class functions** - everything we can do with other types (objects, strings, numbers, booleans) can also be done with functions:
- assigning them to variables,
- passing them around as parameters to other functions,
- creating them on the fly, etc.

Just like any object, function object resides in memory. Though, it's a special type of object because it has all the features of a normal object but has some other special properties. Those are:
- **Name** - though it can be anonymous and not have a name.
- **Code** - where the actual lines of code sit.

The code that we write gets placed in the special property of the function object. So it isn't like the code we write *is* a function. The function *is* an object with other properties. And the code that we write is just one of those properties that we are adding onto it. What is special about that property that it's invocable i.e., when `()` is used the execution context creation and execution happens.

It's important that we have this mental model of functions in our mind. We have to think of functions as objects whose code just happens to be one of the properties. There are other things that functions can have attached to it. And it can be moved around and copied just like other object. We have to think about **functions as more than just containers of code**.

## Function Statements and Function Expressions
**Expression** - a unit of code that results in a value. Statements just do work, but expressions end up creating value. That value doesn't necessarily have to be saved to a variable.
E.g. `1 + 2` is a function expression that return a value of `3`.

Below, in the creation phase the method is put into memory.
```javascript
greet();  // hi

// this is a function statement:
function greet() {
    console.log('hi');
}
```
Whereas below, in the creation phase, JS reserves space for `anonymousGreet` and assigns it `undefined` and in execution phase, the function object is created on the fly. That's why when we invoke the function before definition we get an `Uncaught TypeError: undefined is not a function`.
```javascript
anonymousGreet();  // Uncaught TypeError: undefined is not a function

// this is a function expression:
var anonymousGreet = function() {
    console.log('hi');
}
```

## Conceptual Aside By Value vs By Reference
**By value** (primitives):
```javascript
var a = 3, b = a;
``` 
When we set `b` equals to `a`, equals operator sees these are primitives and creates a new spot in memory and makes a copy of it. `b` and `a` will be both `3` but they are copies sitting on separate spots in memory. So if we change `a = 5` it doesn't affect `b`, it is still `3`.

**By reference** (all objects including functions):
```javascript
var c = {greetings: 'hi'};
var d;
d = c;
```
Equals operator sees there is an object so it simply points to the same spot in memory. After changing a value of an object: `c.greetings = 'hello'` `d` would change as well.

## Objects, Functions, and `this`
```javascript
function a() {
    console.log(this);  // points to the global object (window)
}

var b = {
    name: 'The b object',
    log: function() {
        this.name = 'updated b object';  // points to the b object
        console.log(this);

        var setName = function(newName) {
            this.name = newName;  // points to global object for some reason (fix below)
        }
        setName('Updated again! The b object.');
        console.log(this);
    }
}
b.log();
console.log(this);  // has a new property (name: 'Updated again! The b object.')
```
with the following approach we can access the local `this`, which is a very common pattern.
```javascript
var b = {
    name: 'The b object',
    log: function() {
        var self = this;

        self.name = 'updated b object';
        console.log(self);

        var setName = function(newName) {
            self.name = newName;
        }
        setName('Updated again! The b object.');
        console.log(self);
    }
}
b.log();
```
Another approach is to use an _arrow function_.
```javascript
var b = {
    name: 'The b object',
    log: function() {
        this.name = 'updated b object';
        console.log(this);

        var setName = newName => this.name = newName;
        setName('Updated again! The b object.');
        console.log(this);
    }
}
b.log();
console.log(this);
```

## Conceptual Aside - Arrays  - Collections of Anything
**Arrays** can hold a mix of anything: functions, primitives, objects.
```javascript
var a = [];  // or `new Array();`
var arr = [
    1,
    false,
    {
        name: 'John',
        address: '111 Main St.'
    },
    function(name) {
        console.log(this[4] + " " + name);
    },
    "Hello"
];
arr[3](arr[2].name);
```

## `arguments` and spread
When a function is invoked, JS sets up a keyword called `arguments`. It contains a list of all the values of the parameters passed.
```javascript
function greet(firstName, lastName) {
    // parameters will be `undefined` if they are not passed (hoisting)
    if (arguments.length === 0) {
        console.log('Missing parameters!');
        return;
    }
    console.log(arguments);
}
greet();  // "Missing parameters!"
greet('John');  // ["John"]
greet('John', 'Doe');  // ["John", "Doe"]
```
> **Note**
> It looks and acts like an array but it isn't one.

In ES6 we can do: `function greet(firstName, ...other)` and `other` will be an array that contains the rest of the arguments. It's called a spread parameter.

## Dangerous Aside Automatic Semicolon Insertion
Semicolons are optional in JS because JS engine injects them automatically but it is a bad practice to not put them.
```javascript
function getPerson() {
    return
    {
        firstName: "John"
    };
}
console.log(getPerson());  // undefined
```
when JS sees a carriage return after `return` it automatically inserts a `;` which leads to `undefined` in output. It can be fixed by writing `{` right after `return` i.e., `return {`.

## Immediately Invoked Functions Expressions (IIFEs)
```javascript
var firstname = 'John';

(function(name) {
    
    var greeting = 'Inside IIFE: Hello';
    console.log(greeting + ' ' + name);
    
})(firstname); // IIFE
```
> **Note**
> if we don't keep the IIFE inside `()` it will lead to an `Uncaught SyntaxError: Unexpected token (`. As we only use `()` with expressions, the function will act as an expression and immediately execute.

## Framework Aside IIFEs and Safe Code
**IIFE** creates a new execution context which prevents variable name collisions, a great deal of libraries use this concept.

## Understanding Closures
When a function runs and completes it is removed from execution stack, but variables created and saved in that execution phase are still stored in memory and can be accessed from down the scope.

```javascript
function greet(whattosay) {
    return function(name) {
        console.log(whattosay + ' ' + name);
    };
}

var sayHi = greet('Hi');
sayHi('John');
```
Even though the execution context of `greet` is popped off the stack, the `sayHi` execution context still has a reference to the memory space of its outer environment, `greet`, and when we encounter `whattosay` JS engine goes down the scope chain to access it. This phenomenon of closing in all the variables that it's supposed to have access to is called a _closure_. 

## Closures And Callbacks
**Callback function** is a function passed into another function as an argument, which is then invoked inside the outer function to complete some kind of routine or action.
```javascript
function sayHiLater() {
    var greeting = 'Hi!';
    setTimeout(function() {
        console.log(greeting);
    }, 3000);
}
``` 

## `call()`, `apply()`, and `bind()`
These three methods provide flexibility when it comes to manipulating the execution context of functions.
- **`bind()`**: This method creates a new function that has a specified `this` value and arguments passed to it.

- **`call()`**: Unlike `bind()`, the `call()` method immediately invokes the function with a specified `this` value and arguments passed individually. It allows us to invoke a function with a specific context without creating a new function.

- **`apply()`**: Similar to `call()`, the `apply()` method immediately invokes the function with a specified `this` value and arguments passed as an array or an array-like object. It allows us to invoke a function with a specific context and pass arguments in a more convenient way. The `apply()` method is useful when we have an array of arguments and want to pass them at once.

```javascript
var person = {
    firstName: 'John',
    lastName: 'Doe',
    getFullName: function() {
        return this.firstName + ' ' + this.lastName;
    }
}

var greet = function(greeting) {
    console.log(greeting + ' ' + this.getFullName());
};

greet.bind(person)('Hi');
greet.bind(person, 'Hey')();
greet.call(person, 'Hello');
greet.apply(person, ['Bonjour']);
```

In practice, we can use **`call()`** and **`apply()`** to borrow methods/functions from objects and invoke it with a different object as the context.
```javascript
var person2 = {
    firstName: 'Jane',
    lastName: 'Doe'
}
console.log(person.getFullName.call(person2));
```

**Function currying** - creating a copy of a function but with some preset parameters.
```javascript
function multiply(a, b) {
    return a * b;
}
var multiplyByTwo = multiply.bind(null, 2);  // sets 1st parameter's value to 2 permanently
console.log(multiplyByTwo(4));
```

## Functional Programming
```javascript
function mapForEach(arr, fn) {
    var newArr = [];
    for (var i = 0; i < arr.length; ++i)
        newArr.push(fn(arr[i]));
    return newArr;
}

const arr1 = [1, 2, 3, 4, 5];
const arr2 = mapForEach(arr1, item => item * 2);
const arr3 = mapForEach(arr1, item => item > 2);

var checkPastLimit = (limit, item) => item > limit;
const arr4 = mapForEach(arr1, checkPastLimit.bind(null, 3));

// var checkPastLimitSimplified = limit => ((limit, item) => item > limit).bind(null, limit);
var checkPastLimitSimplified = limit => checkPastLimit.bind(null, limit);
const arr5 = mapForEach(arr1, checkPastLimitSimplified(1));
```
The above **`mapForEach()`** is similar to **`_.map()`** in [underscore.js](underscorejs.org). Its source code is a great source for functional programming concepts.

## Understanding the Prototype
JavaScript implements prototypal inheritance. All JavaScript objects have a prototype property, this property is just a reference to another object called **`__proto__`**. Each object inherits properties and methods of other objects through a prototype. So if we call a property on one object and JS doesn't find it there then JS goes through the prototype chain and looks for it.
```javascript
var person = {
  firstName: '-',
  lastName: '-',
  getFullName: function() {
    return this.firstName + ' ' + this.lastName;
  }
}

var john = {
  firstName: 'John',
  lastName: 'Doe'
}

john.__proto__ = person;
console.log(john.getFullName());  // John Doe

var jane = {
  firstName: 'Jane'
}

jane.__proto__ = person;
console.log(jane.getFullName());  // Jane -
```
> **Warning** :
> The above example is only for demonstration purposes, never set **`__proto__`** property directly as there is some performance penalty.
> **Note** :
> **`this`** points to the object from which the call originated. 

## Everything is an Object (Or a primitive)
All functions, arrays, and objects have a prototype, except the base object. The base object is just called `Object`, it's the very bottom of the prototype chain always.

`function Empty() {}` object is the prototype of all functions, which has functions like `bind()`, `call()`, `apply()`, etc. due to which all functions have access to them.

`[]` is the prototype for all arrays, which has things like `push()`, `pop()`, `indexOf()`, `length`, etc.

## Reflection and Extend
`_.extend(obj, obj2, obj3)` takes all properties and methods of given objects and passes them to the first object. Take a look at its [implementation](https://underscorejs.org/docs/modules/_createAssigner.html).

## Functions Contructors, `new` And The History Of JavaScript

## Function Constructors and `.prototype`
**`.prototype`** is a property that sits in every function in JavaScript but unless we use a function constructor with `new` operator it is never used.

A `.prototype` is **not** *the* prototype of a function object. It is only a prototype of objects created with a `new` keyword.

It's better to put our methods on the `prototype` to save memory space as it gets shared between all objects.

## Dangerous Aside `new` and functions
Any function that we intend to use as a function constructor should be named with a first capital letter. This makes it easier to spot errors in case we would miss `new` keyword.

Although worth mentioning that creating objects with function constructors is going away because of new methods and ES6.

## Conceptual Aside Built-In Function Constructors
JavaScript has some built it function constructors like: `new Number(3)` and `new String('Jason')`.

These constructors look like we are creating primitives but we are not. We are creating objects.

When we use some methods like `.length` on a string, our string is boxed in a `String` object automatically to get access to all its methods.
 
Although it doesn't work like that for numbers and we would need to create `Number` object first.

All these built-in function constructors have a prototype and we can actually add our own methods to it.

```javascript
String.prototype.isLengthGreaterThan = function(limit) {
    return this.length > limit;  
}

console.log("John".isLengthGreaterThan(3));
```

## Dangerous Aside Built-In Function Constructors 2
It's best to avoid using built-in function constructors to create primitives because we are not creating primitives but rather objects.

```javascript
var a = 3;
var b = new Number(3);

a == b // will return true because of coersion
a === b // will return false because we are comparing object with a primitive
```

## Dangerous Aside Arrays and `for`..`in`
For arrays use standard `for` loop or `forEach`, but don't use `for in`. Because arrays are objects with `for in` we could iterate into a prototype.

## `Object.create` and Pure Prototypal Inheritance
```javascript
var person = {
    firstname: 'Default',
    lastname: 'Default',
    greet: function() {
        return 'Hi ' + this.firstname;   
    }
}

var john = Object.create(person);
```

`Object.create(person)` - creates an empty object with its prototype pointing at whatever we passed in as a parameter.

```javascript
john.firstname = 'John';
john.lastname = 'Doe';
```

And we can override properties and methods by adding new ones to this object with the same names.

**Polyfill** - code that adds a feature which the engine may lack.

## ES6 and Classes
JavaScript has classes in ES6. However, it is not like a `class` in other languages. In other languages `class` is like a template. `class` in JS is an object by itself that we use to create other objects.

`class` doesn't change anything how objects and prototypes work under the hood. It just gives we a different way to type. Because of it we may hear JavaScript classes being called syntactic sugar.

**Syntactic sugar** - a different way to type something that doesn't change how it works under the hood.

## Initialization
Large arrays of objects are useful for testing and initialization before we have an actual data to pull from, like a JSON file.

## `typeof`, `instanceof`, and Figuring Out What Something Is
**`typeof`** is an operator (essentially a function) that excepts a parameter and returns a string.

**`instanceof`** will tell we if it has something in its prototype chain by returning a boolean.

```javascript
var a = 3;
console.log(typeof a); // returns a string 'number'

var b = "Hello";
console.log(typeof b); // returns a string 'string'

var c = {};
console.log(typeof c); // returns a string 'object'

var d = [];
console.log(typeof d); // also returns a string 'object', weird!

console.log(Object.prototype.toString.call(d)); // this little trick returns a string '[object Array]'

function Person(name) {
    this.name = name;
}
var e = new Person('Jane');
console.log(typeof e); // also an object

console.log(e instanceof Person); // returns true because Person is down the prototype chain of e

console.log(typeof undefined); // returns undefined, makes sense
console.log(typeof null); // returns an 'object', a bug since, like, forever... 

var z = function() { };
console.log(typeof z); // returns a 'function'
```

## Strict Mode
JavaScript is a more liberal of what it allows.

**`"use strict";`** - enforces more strict rules. E.g. in this mode we must declare var first to use it. In not strict mode if we forget to type `var`, it will still be created on the global object `window`.

We can use use strict at the top of the document or at the top of a function to use strict only inside it's execution context.

## Learning From Other's Good Code
There are many aspects of improving as a developer. One of the most powerful is learning from other's good code. There is a fantastic treasure trove of good code out there to learn from. Tony calls it "an open source education".

It may sound as fun as reading an encyclopedia, but we don't need to spend hours reading the source code. Find some area that's interesting to you. Don't get intimidated by famous libraries and what may seems complex patterns. Look at the structure, see what we could take away and imitate.

This is a great way to learn advanced patterns and concepts in JavaScript. So make a practice to occasionally look at the source code of the library or framework we are using.

## Deep Dive into Source Code jQuery - Part 1
When we're reading code we are not trying to understand how every feature is implemented. First, we'll try to see if we can read the code and learn how it is structured. And if we can learn some techniques and borrow some ideas.

## Deep Dive into Source Code jQuery - Part 2
jQuery has some good code we could borrow for our own projects. It has been developed and watched by many developers so it has some of the best methods and practices.

Inside jQuery there is Sizzle CSS Selector library for handling selectors.

## Deep Dive into Source Code jQuery - Part 3
**Method chaining** - calling one method after another, and each method affects the parent object.

So `obj.method1().method2()` where both methods end up with `this` variable pointing at `obj`.

## Requirements
First of all, before building any application let's think of the requirements. What this app/library should do?

## TypeScript, ES6, and Transpiled Languages
**Transpile** - convert the syntax of one programming language, to another.

In this case, languages that don't really ever run anywhere, but instead are processed by transpilers that generate JavaScript.

**TypeScript** - one of the most popular transpiled languages and is created by Microsoft. The biggest difference that it uses strict types for its variables instead of dynamic types like JavaScript.
