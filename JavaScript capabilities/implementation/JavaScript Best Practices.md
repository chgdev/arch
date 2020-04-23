CHG Enterprise Architecture Principles

[![GitHub version](https://badge.fury.io/gh/chgdev%2Farch.svg)](http://badge.fury.io/gh/chgdev%2Farch)

# CHG JavaScript Development Principles and Best Practices #

This document was written by the Solution Architect team to help developers in the following ways:

- Explain the principles that guide design decisions in CHG JavaScript projects.
- Teach important JavaScript principles that will help developers write better, clearer, more robust, and more maintainable code.
- Help achieve greater consistency in our code, easing developer collaboration.
- Show developers things to look for when reviewing their own or others' code.

Some things that this document is *not*:

- It is not an attempt to set a universal CHG JavaScript code style. Each project chooses a code style that best suits it.
- It is not a set of commandments. Any "rule" in this document can be ignored if there is a sufficiently strong reason to do so.
- It is not written in stone. As technology evolves, this document is intended to evolve with it. Something that is a best practice today may not be tomorrow.

## Core Principles ##
*See the main [CGH architecture principles page](https://github.com/chgdev/arch) for an overview.*

### Data Integrity ###
Write code that hides internal data and implementation details but provides access to needed data and functionality through declared, documented APIs.

### Consumer-Grade ###
- Design for mobile first.
- Create interfaces and interactions that are respectful of the user's objectives. For example, notifications should be delivered via non-modal, dismissable messages, not using `window.alert()`.
- Create user interfaces that react immediately to user actions.
- Do not write only for the "golden path." What will the user see if there's a client-side error? A server-side error? If the server takes a long time to respond? If the server returns no data? If the server returns a boatload of data?

### Long-Term Efficiency and Maximum Value ###
- Maximize interoperability by making your designs consistent with other CHG systems.
- Code to maximize reuse and testability.
- Ruthlessly delete dead code. If you need it back later, that's why we have source control.
- Don't write code that solves problems you don't have yet. Remember YAGNI (You Ain't Gonna Need It).

### Security ###
The client device is not to be trusted.
- Never send any data to the client that you do not want an end user to see.
- All requests must be checked for authentication (the user is identified) and authorization (the user is allowed to perform the requested action).
- Always validate server-side. You can validate client-side as well if you want, but it is for user convenience only and does not replace server-side validation. Never assume that data sent from the client is well-formed and benign.
- Never concatenate user-input data into a command that will be executed against a data store, file system, etc., without proper escaping. The most common example is the SQL injection attack, where user input is directly concatenated into a SQL query string, allowing a malicious user to execute arbitrary SQL commands with carefully-written input. This can be avoided by using parameterized queries or prepared statements, which automatically escape parameters. Don't roll your own escaping; any decent database library or ORM will have built-in protection against injection attacks.
- Whenever input from one user can be displayed to another user, care must be taken to ensure that the data is properly escaped on display to avoid XSS attacks.

### Scale and Agility ###
Employ separation of concerns.
- Keep business logic out of the view layer.
- Avoid tight coupling with other layers.

### Event-driven Architecture ###
We have a large number of systems that interact asynchronously. Applications should be designed to be able to respond to events from other applications.

## Important JavaScript Concepts to Understand ##

### Type Coercion ###
When the interpreter encounters incompatible types, it will attempt to coerce them:
```js
let number = 1;
let string = '1';
let bool = false;
console.dir(number + ''); // '1'
console.dir(+string);     // 1
console.dir(+string++);   // 1
console.dir(string);      // 2
console.dir(+bool);       // 0
console.dir(bool + '');   // 'false'
```

### Truthy and Falsy Values ###
A value is considered "truthy" if it evaluates to `true` when coerced to a boolean, and "falsy" if it evaluates to `false`. There are six falsy values:

- `false`
- `0`
- `''`
- `null`
- `undefined`
- `NaN`

All other values are considered truthy. Conditional expressions are automatically coerced to boolean values, so you can use a truthy or falsy expression and allow the interpreter to coerce it. For example, to check if an array or string is not empty:

```js
if (array.length) { /* ... */ }
if (string) { /* ... */ }
```

Likewise, to check that an array or string is empty:

```js
if (!array.length) { /* ... */ }
if (!string) { /* ... */ }
```

With the `!string` case, be aware that this will also accept other falsy values (`null`, `undefined`, `0`, etc.), so make sure you understand how your code will behave in those cases.

If you must explicitly coerce a truthy/falsy value to its actual boolean equivalent (for example, to work with third-party code that wasn't written to be truthy/falsy tolerant), use the double bang:

```js
let boolValue = !!truthyFalsyValue;
```

### `&&` and `||` ###
Most people think that `&&` and `||` return a boolean value (and for the most part, you can pretend that they do), but reality is a little more complicated:

- `a && b` returns `b` if `a` is truthy, otherwise it returns `a` without evaluating `b`.
- `a || b` returns `b` if `a` is falsy, otherwise it returns `a` without evaluating `b`.

This allows some more powerful usages for these operators than just combining booleans. For example, when assigning a value to a variable, you can use `||` to provide a default when the value is falsy:

Instead of this:
```js
let foo;

if (bar) {
  foo = bar;
} else {
  foo = 'default';
}
```

...or this:
```js
let foo = bar ? bar : 'default';
```

...do this:
```js
let foo = bar || 'default';
```

This is equivalent to the "Elvis operator" in other languages (`?:`).

Another example: check a condition before invoking a function:

Instead of this:
```js
if (ok) {
  doSomething();
}
```

Do this:
```js
ok && doSomething();
```

Also note that `&&` takes precedence over `||`.

### Double Equals (`==`) vs. Triple Equals (`===`) ###
Triple equals (`===`) tests for **strict** equality, meaning that both the type and value you are comparing must be the same for it to resolve to true. Double equals (`==`) tests for **loose** equality. The two values will be compared only after attempting to coerce them to a common type.

```js
console.log(77 === '77');        // false
console.log(77 == '77');         // true
console.log(false === 0);        // false
console.log(false == 0);         // true
console.log(null === undefined); // false
console.log(null == undefined);  // true
```

### Type Checking Weirdness ###
For strings, numbers, booleans, objects, and `undefined`, use the `typeof` operator:
```js
console.log(typeof 'foo');     // string
console.log(typeof 47);        // number
console.log(typeof true);      // boolean
console.log(typeof {});        // object
console.log(typeof undefined); // undefined
```

Note that older versions of JavaScript permitted `undefined` to be redefined. (Yes, really.) This is why it is typically recommended to use `typeof` to test for `undefined`. Starting with ECMAScript 5, `undefined` is now read-only.

You can't use `typeof` to check for `null`:
```js
console.log(typeof null); // object
```

Yes, this is weird, and is widely regarded as a mistake in the original implementation of JavaScript, but here we are. Instead, use the triple equals operator:
```js
if (variable === null) { /* ... */ }
```

Of course, if any falsy value can be treated the same as `null`, then you can simplify the above to:
```js
if (!variable) { /* ... */ }
```

You can't use `===` or `typeof` to determine whether a value is `NaN` (not a number):
```js
console.log(NaN === NaN); // false -- NaN is not equal to anything, even itself
console.log(typeof NaN);  // number -- Yes, "Not a Number" is a number
```

The only way to determine whether a value is `NaN` is to use the `isNaN()` function:
```js
console.log(isNaN(NaN)); // true
```

Arrays are not a fundamental type in JavaScript, so `typeof` will not help you determine whether something is an array:
```js
console.log(typeof []); // object
```

Instead, use `Array.isArray()`:
```js
console.log(Array.isArray([])); // true
```

To check for the existence of a property *anywhere* in an object's prototype chain, use `in`:
```js
if ('prop' in object) { /* ... */ }
```

To check for the existence of a property only directly on that object, use `Object.hasOwnProperty()`:
```js
if (object.hasOwnProperty('prop')) { /* ... */ }
```

To check the type of a DOM node, use `Element.nodeType`:
```js
if (elem.nodeType === 1) { /* ... */ }
```

### `null` vs. `undefined` ###
![Image illustrating the difference between `0`, `null`, and `undefined`: The first image is captioned `0` and shows a toilet paper dispenser with an empty roll on it. The second image is captioned `null` and shows the same dispenser, but with no roll on it. The third image is captioned `undefined` and shows a bare wall with no dispenser.](https://pbs.twimg.com/media/DusCOfyXcAA9_F7.jpg)

In JavaScript, `undefined` typically means that a variable has been declared but not assigned. However, you can also explicitly set a variable to be `undefined`:
```js
let a;
console.log(a); // undefined
a = undefined;
console.log(a); // undefined
```

You'll also get `undefined` when looking up non-existent properties on an object:
```js
let b = {};
console.log(b.fake); // undefined
```

A `null` value is different: the variable has been assigned and so it is not `undefined`:
```js
let c = null;
console.log(c); // null
```

One place where the distinction comes into play is when using default argument values. An omitted argument is considered `undefined`, and explicitly passing `undefined` is functionally equivalent to omitting it. But `null` is considered to be a defined value, and so does not result in the default value being used:
```js
function foo(bar = 'bar') {
  console.log('Value is ' + bar);
}
foo();          // 'Value is bar'
foo(undefined); // 'Value is bar'
foo(null);      // 'Value is null'
```

## Best Practices ##

### Use strict mode. ###
Putting the `'use strict';` directive at the beginning of your code will force it into *[strict mode](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Strict_mode)*, which, among other things, causes several conditions that would silently fail in "sloppy mode" to throw errors. However, make sure that you do so within a scope limited to your own code, not globally, or you may inadvertently affect other code. (Wrapping your code in a self-executing function is one way; see [Avoid polluting the global scope](#avoid-polluting-the-global-scope-) below.)

### Maximize code readability. ###
- CHG does not have a universal JavaScript coding style guide for all of its projects. However, any one individual project should have a consistent code style. It is recommended that each project link to a code style guide in its `README.md` file, and that all contributors to that project follow it.
- If you don't have a particular code style guide chosen for a new project, [Google's JavaScript Style Guide](https://google.github.io/styleguide/jsguide.html) is a good one to use.
- This best practices guide generally avoids being opinionated about code style, except in cases where a style choice can have bug-inducing consequences.
- Use meaningful names for variables and functions that communicate intent.
- We'd rather have code that is verbose but clear than code that is terse and obtuse.
- Use consistent naming conventions.
- Use appropriate case:
  - `camelCase`: variable and function names
  - `PascalCase`: constructor functions, enumerations
  - `ALL_CAPS_WITH_UNDERSCORES`: global constants
- Favor clarity over cleverness.
- Prefer self-documenting code, meaning code that is written so clearly that comments are not usually needed.
- **Do** use comments:
  - when further clarification is helpful.
  - to explain to other developers how to use your code or what circumstances may cause errors to be thrown.
  - to document decisions that may otherwise be reversed later by a well-meaning developer.
  - to provide links to relevant reference material.
  - that explain "why" rather than "what".
- Break up large code blocks.
- A function should be short, to the point, and do **one thing**.
- Higher-level functions should be listed before lower-level ones.
- Do not over-architect your code. Increased complexity usually comes with decreased readability and maintainability. Write the simplest thing that could possibly work, then test to see if it meets your correctness, performance, and resource requirements. Add complexity only as needed.

  > Premature optimization is the root of all evil. —Donald Knuth

  > Debugging is twice as hard as writing the code in the first place. Therefore, if you write the code as cleverly as possible, you are, by definition, not smart enough to debug it. —Brian W. Kernighan

### Avoid polluting the global scope. ###
Scripts which create global variables can run into naming conflicts. You can employ closures, self-executing functions, and modules to avoid creating global variables. A self-executing function looks like this:
```js
(function() {
  let foo = 'bar';
  // do stuff
})();
// foo is undefined outside the self-executing function
```

### Avoid using too many parameters in a function. ###
Encapsulate them in an object instead.

Instead of this:
```js
function myFn(foo1, bar1, baz1, foo2, bar2, baz2) {
  // do something
}
```

...do this:
```js
function myFn(config) {
  // do something
}
```

If you desire, you can use object destructuring to automatically unpack the configuration object's properties inside your function:
```js
function myFn({ foo1, bar1, baz1, foo2, bar2, baz2 }) {
  // do something
}
```

### Beware of variable shadowing. ###
Shadowing is when a variable is declared with the same name as one from a larger scope. While it is legal to do this, it can lead to confusion and should be avoided.

Instead of this:
```js
function foo(i) {
  for (let i = 0; i < 10; i++) {
    // The variable name i now refers to the one declared in the for loop
    console.log(i);
  }

  // The variable name i now refers to the argument in the function declaration
  console.log(i);
}
```

...do this:
```js
function foo(bar) {
  for (let i = 0; i < 10; i++) {
    // Now there's no confusion between the two
    console.log(i);
  }

  console.log(bar);
}
```

### Use semicolons. ###
While JavaScript does have rules that tell the interpreter where semicolons should be implied to exist, it is clearer and safer to just use them explicitly.

### Always explicitly declare code blocks with curly braces, even if they're only one line long. ###
Instead of this:
```js
while (condition)
  doSomething();
```

...do this:
```js
while (condition) {
  doSomething();
}
```

Why? This is why:

```js
while (condition)
  doSomething();
  doSomethingElse();
```

In the code snippet above, `doSomethingElse()` is **outside** the loop. 😱

### Don't put opening curly braces on their own line. ###
This isn't just a stylistic preference; automatic semicolon insertion in JavaScript can cause different results depending on the placement of your curly braces. Consider the following example:
```js
function test() {
  return
  { // <-- curly brace on new line
    bar: 'It worked.'
  };
}

let foo = test();

try {
  alert(foo.bar);
} catch (e) {
  alert('Error thrown, foo is ' + typeof r);
}
```

When the above code is executed, the interpreter inserts a semicolon immediately after the `return` keyword in `test()`, and the object literal after it becomes dead code. As a result, `test()` returns `undefined` instead of the expected object, and the line that attempts to evaluate `foo.bar` throws an error. Compare that against this code, which is identical except that the opening curly brace has been moved to be on the same line as `return`:
```js
function test() {
  return { // <-- curly brace on same line
    bar: 'It worked.'
  };
}

let foo = test();

try {
  alert(foo.bar);
} catch (e) {
  alert('Error thrown, foo is ' + typeof r);
}
```

This time, `test()` returns the expected object, and no error is thrown.

### Use `const` if you can, and `let` if you must. Don't use `var`. ###
Variables declared with `var` are function-scoped and [hoisted](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting), which can be confusing. Variables declared with `const` and `let` are block-scoped and not hoisted. Additionally, `const`s can't be reassigned, which communicates your expectations more clearly and permits optimization by the interpreter.

### Always use triple equals (`===`). ###
A lot of unexpected weirdness happens when you use loose equality checks.

### Write code that is truthy/falsy tolerant. ###
Instead of this:
```js
function foo(bar) {
  if (bar === false) {
    // do something
    // Not invoked if passed a falsy value that isn't false
  }
}
```

...do this:
```js
function foo(bar) {
  if (!bar) {
    // do something
    // Works for any falsy value passed in
  }
}
```

### Use guard clauses and early `return`s rather than nested `if`s to decrease nesting and improve readability. ###
Instead of this:
```js
function getPayAmount() {
  let result;

  if (isDead) {
    result = deadAmount();
  } else {
    if (isSeparated) {
      result = separatedAmount();
    } else {
      if (isRetired) {
        result = retiredAmount();
      } else {
        result = normalPayAmount();
      }
    }
  }

  return result;
}
```

...do this:
```js
function getPayAmount() {
  if (isDead) {
    return deadAmount();
  }

  if (isSeparated) {
    return separatedAmount();
  }

  return isRetired ? retiredAmount() : normalPayAmount();
}
```

### Use object and array literal syntax. ###
Instead of this:
```js
let cow = new Object();
cow.color = 'brown';
cow.commonQuestion = 'How now?';
cow.moo = function() {
  console.log('Moo');
}
cow.feet = 4;
let neoswingBands = new Array();
neoswingBands[0] = 'Big Bad Voodoo Daddy';
neoswingBands[1] = 'The Brian Setzer Orchestra';
neoswingBands[2] = 'Cherry Poppin\' Daddies';
neoswingBands[3] = 'Squirrel Nut Zippers';
neoswingBands[4] = 'Big Tubba Mista';
```

...do this:
```js
let cow = {
  color: 'brown',
  commonQuestion: 'How now?',
  moo: function() {
    console.log('Moo');
  },
  feet: 4
};
let neoswingBands = [
  'Big Bad Voodoo Daddy',
  'The Brian Setzer Orchestra',
  'Cherry Poppin\' Daddies',
  'Squirrel Nut Zippers',
  'Big Tubba Mista'
];
```

### Create "enum" objects. ###
JavaScript doesn't support enumerations, but you can fake it with objects.

Instead of this:
```js
const SIZE_SMALL = 1;
const SIZE_MEDIUM = 2;
const SIZE_LARGE = 3;

let size = SIZE_MEDIUM;
```

...do this:
```js
const SizeEnum = {
  SMALL: 1,
  MEDIUM: 2,
  LARGE: 3
};

let size = SizeEnum.MEDIUM;
```

This practice allows you to attach additional properties or logic either to individual entries or the enum object itself, ensuring that all related logic is found in one place:

```js
const SizeEnum = {
  SMALL: {
    value: 1,
    coffeeCupName: 'tall'
  },
  MEDIUM: {
    value: 2,
    coffeeCupName: 'grande'
  },
  LARGE: {
    value: 3,
    coffeeCupName: 'venti'
  },
  fromCoffeeCupName: name => {
    name = name.trim().toLowerCase();

    for (let i = 0; i < SizeEnum.VALUES; i++) {
      let size = SizeEnum.VALUES[i];

      if (name === size.coffeeCupName) {
        return size;
      }
    }

    return undefined;
  }
};
SizeEnum.VALUES = [ SizeEnum.SMALL, SizeEnum.MEDIUM, SizeEnum.LARGE ];
```

### Use ternary notation for short conditionals. ###
Instead of this:
```js
let direction;

if (x > 100) {
  direction = 1;
} else {
  direction = -1;
}
```

...do this:
```js
let direction = x > 100 ? 1 : -1;
```

Do not use them for long conditionals, and don't nest them.

### Avoid using `switch()`, prefer the delegation pattern instead. ###
Use a delegation object to emulate an enumeration. This permits you to centralize the logic around an enumeration of values, so that if a new value gets added in the future, you don't have to search the entire code base to discover where they're being used.

Instead of this:
```js
switch(x) {
case 'foo':
  // do something
  break;
case 'bar':
  // do something
  break;
default:
  // do something
}
```

...do this:
```js
const Delegator = {
  foo: () => {
    // do something
  },
  bar: () => {
    // do doSomething
  },
  _default: () => {
    // do something
  }
}
// ...
(Delegator[x] || Delegator._default)();
```

### If you do use `switch()`, end every case with a `break` and always include a `default` clause. ###
Note that it is okay to have several cases together if they all do the exact same thing:
```js
switch(x): {
  case 'foo':
  case 'bar':
    /* do something */
    break;
  case 'baz':
    /* do something else */
    break;
  default:
    /* do default case */
}
```

### Beware `for(x in y)` iterating inherited properties. ###
This can cause unexpected behaviors. Consider filtering properties as they're iterated.

Instead of this:
```js
for (prop in obj) {
  // do something
}
```

...do this:
```js
for (key in obj) {
  if (obj.hasOwnProperty(key)) {
    // do something
  }
}
```

### `eval()` is evil. ###
Why?

- It's hard to be sure that you aren't creating a code injection vulnerability.
- It tends to be slow because `eval()`ed code can't be precompiled.
- `eval()`ed code is hard to debug.

`eval()` is like explosives: It might fix your problem, but it's usually overkill and you might not be prepared for the collateral damage. You should never use it unless you are absolutely sure that users cannot inject their own content into the string being `eval()`ed, and probably not even then. It's bad enough in a browser context; it's even worse in Node.

It used to be common to see people parsing JSON by passing it to `eval()`. JavaScript now provides the `JSON.parse()` method for that purpose.

### Throw `Error` objects. ###
While ECMAScript allows you to use any object with `throw`, you should always throw `Error` (or another error type) because:
- Exception handlers can always expect an object that conforms to the `Error` object's specifications.
- It provides programmatic access to the stack trace.
- It provides more useful error messages in older browsers.

Instead of this:
```js
throw 'Error message';
```

...do this:
```js
throw Error('Error message');
```

### Distinguish between operational and programmer errors. ###
For example, errors caused by invalid input are expected and reflect correct application behavior. Unexpected errors are caused by bugs or environment issues and require attention. Ensure that the software treats these two kinds of errors differently.

### Always include the radix argument when calling `parseInt()`. ###
Omitting the radix argument does not cause the interpreter to assume base 10. Rather, it attempts to auto-detect the radix:

- If the string begins with `0x` or `0X`, assume base 16.
- If the string begins with `0` but not `0x` or `0X`, behavior is implementation-dependent. (ECMAScript 5 assumes base 10, but earlier implementations may assume base 8.)
- Otherwise, assume base 10.

### Use native methods when possible. ###
They're faster and often easier to read.

Instead of this:
```js
let arr = [ 'item 1', 'item 2', 'item 3' /* ,  ... */ ];
let list = '';
arr.forEach((item, i) => {
  list += (i !== 0 ? ', ' : '') + item;
});
```

...do this:
```js
let arr = [ 'item 1', 'item 2', 'item 3' /* , ... */ ];
let list = arr.join(', ');
```

Libraries such as jQuery, lodash, and underscore offer a variety of utilities to do useful things, but as the JavaScript language specification has evolved, many of these capabilities can now be performed natively. Don't use utility code to do things that can be done relatively easily with native code.

### Avoid using non-standard or experimental language features or APIs, and don't use ones which are obsolete or deprecated. ###
Using these language features or APIs makes our code more brittle and its behavior less consistent. If you feel you must use something which is non-standard or experimental, detect support for the feature and provide graceful handling in case it is absent.

### Do not monkey patch native types except to polyfill an existing ECMAScript standard. ###
[Monkey patching](https://www.audero.it/blog/2016/12/05/monkey-patching-javascript/) is modifying a prototype to add or replace one or more of its functions. Because it makes changes to types that may be unexpected, you should only do this to provide functionality defined by a standard not yet supported by the interpreter, because the method signature and expected behavior are already well-defined. This particular kind of monkey patching is referred to as a polyfill. Note that a polyfill should be written so that it is only applied when the functionality does not already exist.

For example, here's how to polyfill `String.includes()`:

```js
String.prototype.includes = String.prototype.includes || (search, start) => {
  'use strict';
  if (typeof start !== 'number') {
    start = 0;
  }

  if (start + search.length > this.length) {
    return false;
  }

  return this.indexOf(search, start) !== -1;
};
```

### Make use of promises for asynchronous calls. ###
Instead of this:
```js
asyncFunc1((err, result1) => {
  asyncFunc2(result1, (err, result2) => {
    asyncFunc3(result2, (err, result3) => {
      console.log(result3);
    });
  });
});
```

...do this:
```js
asyncFuncPromise1()
  .then(asyncFuncPromise2)
  .then(asyncFuncPromise3)
  .then(result => console.log(result))
  .catch(err => console.error(err))
```

### Debounce server calls that may be invoked multiple times in rapid succession. ###
A common case for this is "search as you type." A naïve implementation will execute a search query with every keystroke. The faster you type, the more rapid-fire the requests. What would be more sensible is to introduce a short delay before querying, and if another keystroke is received before the delay expires, reset the timer. That way, no request is sent until the user pauses, and the server is not bombarded. The npm [debounce](https://www.npmjs.com/package/debounce) package implements debouncing for you.

### Use tools to check for vulnerabilities, errors, and code style problems. ###
Some examples of tools that can help with this include [ESLint](https://eslint.org/) and [SonarQube](https://www.sonarqube.org/). Your chosen tool should be integrated with your build process so that issues are discovered and reported automatically. You should also be testing for vulnerable (`npm audit`) or outdated (`npm outdated`) dependencies.

## Best Practices for a Browser Environment ##

### Don't use JavaScript to do HTML's or CSS's job. ###
HTML is for content. CSS is for presentation. JavaScript is for behavior. Avoid doing in JavaScript what ought to be done in HTML or CSS. For example, if you build a site's navigation with JavaScript, it will be unavailable to spiders that are trying to index your content.

### Avoid concatenating HTML whenever possible. ###
Use the build-in templating mechanisms supported by your view library (Angular, React, etc.), or if working against the native API, use `textContent` to write text into a node:
```js
document.getElementById('foo').textContent = value;
```

To dynamically create elements, use the `document.createElement()` method:
```js
let link = document.createElement('a');
link.href = url;
link.textContent = 'Link text';
document.body.appendChild(link);
```

Allowing raw HTML to be concatenated with user-supplied values creates a vector for XSS attacks.

### Data from input fields is always a string; coerce it to the appropriate type. ###
This is true even for input types which you might think should provide a different data type, such as `<input type="range">`. Protip: You can coerce a string to an integer with the unary `+` operator:

```js
let foo = +document.getElementById('foo-input').value;
```

### Store element references. ###
Looking up elements incurs a performance penalty. Store references to frequently-used elements for later re-use.

## Best Practices for a Node Environment ##

### Create private npm packages for common utilities. ###
This ensures that common code is only written once and can be updated in one place, instead of being copied across multiple applications.

### Configuration should be environment aware, secure, and hierarchical. ###
- Keys should be read from the file AND environment variables. This permits you to use environment variables to override values from a configuration file.
- Secrets should be kept out of committed code.
- Configuration should be hierarchical to make things easier to find.

Several packages make it easy to do this, including [rc](https://www.npmjs.com/package/rc), [nconf](https://www.npmjs.com/package/nconf), and [config](https://www.npmjs.com/package/config).

### Set up centralized error handling. ###
Errors aren't only emitted by requests. For example, you may have cron jobs that need to report errors as well. Ensure that your error handling isn't buried in Express middleware.

### Use a mature logging platform. ###
Logging platforms such as [winston](https://www.npmjs.com/package/winston), [Bunyan](https://www.npmjs.com/package/bunyan), or [log4js](https://www.npmjs.com/package/log4js) are much more powerful than `console.log()`.

### Register error handlers to the `process.unhandledException` and `process.unhandledRejection` events. ###
Unhandled rejected promises don't count as unhandled exceptions, so make sure you're listening for both events. Servers should not crash due to invalid input; otherwise, this opens a denial-of-service attack vector.

### Validate API inputs. ###
Remember that the client is not to be trusted. Validate all data passed to your API. Libraries such as [joi](https://www.npmjs.com/package/joi) can help with this.

### Make sure all your server's cores are being used. ###
Node only runs on a single core. To leverage multiple cores, you will need to use something like the [cluster module](https://www.npmjs.com/package/cluster) or deploy using a Docker cluster.

### Offload CPU-intensive computation to a different application (at least until the worker threads module is stable). ###
Node is single-threaded, so doing computationally intensive work will block the process from serving other requests. [Worker threads](https://nodejs.org/api/worker_threads.html) are a thing, but they're experimental at this writing and should not be considered production-ready.

### Design your applications to be stateless. ###
Stateless applications can be killed with impunity when they're acting up.

### Set `NODE_ENV`. ###
The `NODE_ENV` environment variable should be set to `'production'` or `'development'` as appropriate. This enables optimizations in production mode that will otherwise significantly slow down your app.

### Create build pipelines that enable automatic, atomic, zero-downtime deployments. ###
Continuous integration tools and deployment technologies such as Docker, as well as building your applications to be stateless, will greatly help with this.

### Run Node as a non-root user. ###
If an attacker does gain remote execution privileges, this will mitigate the potential damage.

### Never load files using a variable that could contain user-supplied data. ###
This would include, for example, `fs.readFile()` or loading modules with `require`.

### Do not include sensitive environment details in error payloads that are sent to the client. ###
File paths, third-party modules in use, stack traces, etc. should never be exposed to the client, as they could reveal useful information to an attacker. However, this information is often helpful in development, so you may want to check the `NODE_ENV` environment variable and only return those details to the client if it's not `'production'`.
