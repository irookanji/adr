# JavaScript Code Style Guideline

Based on:

AirBnb:<br/>
https://github.com/airbnb/javascript
<br/>
https://www.npmjs.com/package/eslint-config-airbnb

Google:<br/>
https://google.github.io/styleguide/jsguide.html
<br/>
https://www.npmjs.com/package/eslint-config-google

Mozilla:<br/>
https://developer.mozilla.org/en-US/docs/MDN/Writing_guidelines/Writing_style_guide/Code_style_guide/JavaScript

### Array

Use return statements in array method callbacks. It’s ok to omit the return if the function body consists of a single statement returning an expression without side effects.
eslint: array-callback-return.


// bad
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
    const flatten = acc.concat(item);
});
// good
[[0, 1], [2, 3], [4, 5]].reduce((acc, item, index) => {
    const flatten = acc.concat(item);
    return flatten;
});
// bad
inbox.filter((msg) => {
    const { subject, author } = msg;
    if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
    } else {
        return false;
    }
});
// good
inbox.filter((msg) => {
    const { subject, author } = msg;
    if (subject === 'Mockingbird') {
        return author === 'Harper Lee';
    }
    return false;
});


---

Don’t use iterators. Prefer JavaScript’s higher-order functions instead of loops like for-in or for-of. eslint: no-iterator no-restricted-syntax

Why? This enforces our immutable rule. Dealing with pure functions that return values is easier to reason about than side effects.

Use map() / every() / filter() / find() / findIndex() / reduce() / some() / ... to iterate over arrays, and Object.keys() / Object.values() / Object.entries() to produce arrays so you can iterate over objects.


// bad
const increasedByOne = [];
for (let i = 0; i < numbers.length; i++) {
    increasedByOne.push(numbers[i] + 1);
}
// best
const increasedByOne = numbers.map((num) => num + 1);


---

### Function

Use object destructuring when accessing and using multiple properties of an object. eslint: prefer-destructuring

Why? Destructuring saves you from creating temporary references for those properties, and from repetitive access of the object. Repeating object access creates more repetitive code, requires more reading, and creates more opportunities for mistakes. Destructuring objects also provides a single site of definition of the object structure that is used in the block, rather than requiring reading the entire block to determine what is used.


// bad
function getFullName(user) {
    const firstName = user.firstName;
    const lastName = user.lastName;
    return `${firstName} ${lastName}`;
}
// good
function getFullName(user) {
    const { firstName, lastName } = user;
    return `${firstName} ${lastName}`;
}
// best
function getFullName({ firstName, lastName }) {
    return `${firstName} ${lastName}`;
}


---

If the function body consists of a single statement returning an expression without side effects, omit the braces and use the implicit return. Otherwise, keep the braces and use a return statement. eslint: arrow-parens, arrow-body-style

Why? Syntactic sugar. It reads well when multiple functions are chained together.


// bad
// No implicit return with side effects
let result = []
[1,2,3].map((number) => {
    const nextNumber = number + 1;
    result.push(number)
})
// good
[1, 2, 3].map((number) => {
    const nextNumber = number + 1;
    return `A string containing the ${nextNumber}.`;
});


---

When programmatically building up strings, use template strings instead of concatenation. eslint: prefer-template template-curly-spacing

Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.


// bad
function sayHi(name) {
    return 'How are you, ' + name + '?';
}
// good
function sayHi(name) {
    return `How are you, ${name}?`;
}


---

Use default parameter syntax rather than mutating function arguments.

// bad
function handleThings(opts) {
    // No! We shouldn’t mutate function arguments.
    // Double bad: if opts is falsy it'll be set to an object which may
    // be what you want but it can introduce subtle bugs.
    opts = opts || {};
    // ...
}
// good
function handleThings(opts = {}) {
    // ...
}


---

Never mutate parameters. eslint: no-param-reassign

Why? Manipulating objects passed in as parameters can cause unwanted variable side effects in the original caller.


// bad
function f1(obj) {
    obj.key = 1;
     // ...
}
// good
function f2(obj) {
    const key = obj.hasOwnProperty('key') ? obj.key : 1;
     // ...
}


---

Avoid confusing arrow function syntax (=>) with comparison operators (<=, >=). eslint: no-confusing-arrow


// bad
const itemHeight = (item) => item.height <= 256 ? item.largeSize : item.smallSize;
// bad
const itemHeight = (item) => item.height >= 256 ? item.largeSize : item.smallSize;
// good
const itemHeight = (item) => (item.height <= 256 ? item.largeSize : item.smallSize);
// good
const itemHeight = (item) => {
    const { height, largeSize, smallSize } = item;
    return height <= 256 ? largeSize : smallSize;
};


---

Assign variables where you need them, but place them in a reasonable place.

Why? let and const are block scoped and not function scoped.


// bad - unnecessary function call
function checkName(hasName) {
    const name = getName();
    if (hasName === 'test') {
        return false;
    }
    if (name === 'test') {
        return false;
    }
    return name;
}
// good
function checkName(hasName) {
    if (hasName === 'test') {
        return false;
    }
    const name = getName();
    if (name === 'test') {
        return false;
    }
    return name;
}


---

If an if block always executes a return statement, the subsequent else block is unnecessary. A return in an else if block following an if block that contains a return can be separated into multiple if blocks. eslint: no-else-return


// bad
function foo() {
    if (x) {
        return x;
    } else {
        return y;
    }
}
// good
function foo() {
    if (x) {
        return x;
    }
    return y;
}


---

Don't use selection operators in place of control statements.


// bad
!isRunning && startRunning();
// good
if (!isRunning) {
    startRunning();
}


---

Don’t save references to this. Use arrow functions or Function#bind.


// bad
function foo() {
    const self = this;
    return function () {
        console.log(self);
    };
}
// bad
function foo() {
    const that = this;
    return function () {
        console.log(that);
    };
}
// good
function foo() {
    return () => {
        console.log(this);
    };
}


---

If the property/method is a boolean, use isVal() or hasVal()


// bad
if (!dragon.age()) {
    return false;
}
// good
if (!dragon.hasAge()) {
    return false;
}


---

Exporting static container classes and objects

Do not export container classes or objects with static methods or properties for the sake of namespacing.


// bad: Container is an exported class that has only static methods and fields.
export class Container {
/\*_ @return {number} _/
    static bar() {
        return 1;
    }
}
/\*_ @const {number} _/
Container.FOO = 1;
Instead, export individual constants and functions:
// good
/\*_ @return {number} _/
export function bar() {
    return 1;
}
export const /\*_ number _/ FOO = 1;


---

Wrapper objects for primitive types

Never use new on the primitive object wrappers (Boolean, Number, String, Symbol), nor include them in type annotations.


// bad
const /\*_ Boolean _/ x = new Boolean(false);
if (x) alert(typeof x)// alerts 'object' - WAT?
// The wrappers may be called as functions for coercing (which is preferred over using + or concatenating the empty string) or creating symbols.
// good
const /\*_ boolean _/ x = Boolean(0);
if (!x) alert(typeof x);// alerts 'boolean', as expected


---

### Conditional operator

There is one notable case to keep in mind for the if...else control statements. If the if statement ends with a return, do not add an else statement.

Continue right after the if statement. Write:


// bad
if (test) {
    // Perform something if test is true
    // …
    return;
} else {
    // Perform something if test is false
    // …
}
// good
if (test) {
    // Perform something if test is true
    // …
    return;
}
// …


---

When you want to store to a variable a literal value depending on a condition, use a conditional (ternary) operator instead of an if...else statement. This rule also applies when returning a value. Write:


//bad
let x;
if (condition) {
    x = 1;
} else {
    x = 2;
}
//good
const x = condition ? 1 : 2;


---

The conditional operator is helpful when creating strings to log information. In such cases, using a regular if...else statement leads to long blocks of code for a side operation like logging, obfuscating the central point of the example.

For collections, avoid adding the type such as list, array, queue in the name. Use the content name in the plural form. For example, for an array of cars, use cars and not carArray or carList. There may be exceptions, like when you want to show the abstract form of a feature without the context of a particular application.

For primitive values, use camelCase, starting with a lowercase character. Do not use \_. Use concise, human-readable, and semantic names where appropriate. For example, use currencyName rather than currency_name.

Avoid using articles and possessives. For example, use car instead of myCar or aCar. There may be exceptions, like when describing a feature in general without a practical context.

Use variable names as shown here:


// bad
const s = d / t;
// good
const speed = distance / time;
const playerScore = 0;


Switch statements can be a little tricky.

Don't add a break statement after a return statement in a specific case. Instead, write return statements like this:


// bad
switch (species) {
    case "chicken":
        return farm.shed;
        break;
    case "horse":
        return corral.entry;
        break;
    default:
        return "";
}
// good
switch (species) {
    case "chicken":
        return farm.shed;
    case "horse":
        return corral.entry;
    default:
        return "";
}


---

### Naming

Avoid single letter names. Be descriptive with your naming. eslint: id-length


// bad
function q() {
    // ...
}
// good
function query() {
    // ...
}


---

Use camelCase when naming objects, functions, and instances. eslint: camelcase


// bad
const OBJEcttsssss = {};
const this_is_my_object = {};
function c() {}
// good
const thisIsMyObject = {};
function thisIsMyFunction() {}


---

Do not use trailing or leading underscores. eslint: no-underscore-dangle

Why? JavaScript does not have the concept of privacy in terms of properties or methods. Although a leading underscore is a common convention to mean “private”, in fact, these properties are fully public, and as such, are part of your public API contract. This convention might lead developers to wrongly think that a change won’t count as breaking, or that tests aren’t needed. tl;dr: if you want something to be “private”, it must not be observably present.


// bad
this.__firstName__ = 'Panda';
this.firstName_ = 'Panda';
this._firstName = 'Panda';
// good
this.firstName = 'Panda';
// good, in environments where WeakMaps are available
// see https://kangax.github.io/compat-table/es6/#test-WeakMap
const firstNames = new WeakMap();
firstNames.set(this, 'Panda');


### Importing

Do not include JavaScript filename extensions eslint:

Why? Including extensions inhibits refactoring, and inappropriately hardcodes implementation details of the module you're importing in every consumer.


// bad
import foo from './foo.js';
import bar from './bar.jsx';
import baz from './baz/index.jsx';
// good
import foo from './foo';
import bar from './bar';
import baz from './baz';


---
