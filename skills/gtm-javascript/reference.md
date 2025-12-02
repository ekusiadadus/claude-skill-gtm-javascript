# ES5 Conversion Reference

Complete reference for converting ES6+ JavaScript to ES5-compatible code for GTM Custom HTML tags.

## Variable Declarations

### const / let → var

```javascript
// ES6 (PROHIBITED)
const API_KEY = 'abc123';
let count = 0;
let items = [];

// ES5 (REQUIRED)
var API_KEY = 'abc123';
var count = 0;
var items = [];
```

**Note:** `var` is function-scoped, not block-scoped. Be aware of hoisting behavior.

## Functions

### Arrow Functions → Function Expressions

```javascript
// ES6 (PROHIBITED)
const add = (a, b) => a + b;
const greet = () => { return 'Hello'; };
const double = x => x * 2;
arr.map(item => item.id);
arr.filter(x => x > 0);

// ES5 (REQUIRED)
var add = function(a, b) { return a + b; };
var greet = function() { return 'Hello'; };
var double = function(x) { return x * 2; };
arr.map(function(item) { return item.id; });
arr.filter(function(x) { return x > 0; });
```

### Default Parameters

```javascript
// ES6 (PROHIBITED)
function greet(name = 'Guest', greeting = 'Hello') {
  return greeting + ', ' + name;
}

// ES5 (REQUIRED)
function greet(name, greeting) {
  name = name || 'Guest';
  greeting = greeting || 'Hello';
  return greeting + ', ' + name;
}
```

**Warning:** The `||` pattern treats `0`, `''`, `false`, and `null` as falsy. For explicit undefined checks:

```javascript
function greet(name, greeting) {
  name = (typeof name !== 'undefined') ? name : 'Guest';
  greeting = (typeof greeting !== 'undefined') ? greeting : 'Hello';
  return greeting + ', ' + name;
}
```

### Rest Parameters

```javascript
// ES6 (PROHIBITED)
function sum(...numbers) {
  return numbers.reduce((a, b) => a + b, 0);
}

// ES5 (REQUIRED)
function sum() {
  var numbers = Array.prototype.slice.call(arguments);
  var total = 0;
  for (var i = 0; i < numbers.length; i++) {
    total += numbers[i];
  }
  return total;
}
```

## String Handling

### Template Literals → Concatenation

```javascript
// ES6 (PROHIBITED)
var message = `Hello ${name}, you have ${count} items`;
var multiline = `Line 1
Line 2
Line 3`;
var expr = `Total: ${price * quantity}`;

// ES5 (REQUIRED)
var message = 'Hello ' + name + ', you have ' + count + ' items';
var multiline = 'Line 1\n' +
  'Line 2\n' +
  'Line 3';
var expr = 'Total: ' + (price * quantity);
```

## Object and Array Handling

### Destructuring → Individual Access

```javascript
// ES6 (PROHIBITED)
const { name, age, city } = person;
const [first, second, ...rest] = array;
const { data: { items } } = response;

// ES5 (REQUIRED)
var name = person.name;
var age = person.age;
var city = person.city;
var first = array[0];
var second = array[1];
var rest = array.slice(2);
var items = response.data.items;
```

### Spread Operator

```javascript
// ES6 (PROHIBITED)
var newArr = [...oldArr, 4, 5];
var merged = {...obj1, ...obj2};
dataLayer.push(...events);
Math.max(...numbers);

// ES5 (REQUIRED)
var newArr = oldArr.concat([4, 5]);
var merged = {};
for (var key in obj1) {
  if (obj1.hasOwnProperty(key)) merged[key] = obj1[key];
}
for (var key in obj2) {
  if (obj2.hasOwnProperty(key)) merged[key] = obj2[key];
}
dataLayer.push.apply(dataLayer, events);
Math.max.apply(Math, numbers);
```

### Object Method Shorthand

```javascript
// ES6 (PROHIBITED)
var obj = {
  name,
  greet() { return 'Hello'; },
  calculate(x) { return x * 2; }
};

// ES5 (REQUIRED)
var obj = {
  name: name,
  greet: function() { return 'Hello'; },
  calculate: function(x) { return x * 2; }
};
```

### Computed Property Names

```javascript
// ES6 (PROHIBITED)
var key = 'dynamicKey';
var obj = {
  [key]: 'value',
  ['prefix_' + id]: data
};

// ES5 (REQUIRED)
var key = 'dynamicKey';
var obj = {};
obj[key] = 'value';
obj['prefix_' + id] = data;
```

## Loops and Iteration

### for-of → Traditional for Loop

```javascript
// ES6 (PROHIBITED)
for (const item of items) {
  console.log(item);
}

for (const [key, value] of Object.entries(obj)) {
  console.log(key, value);
}

// ES5 (REQUIRED)
for (var i = 0; i < items.length; i++) {
  var item = items[i];
  console.log(item);
}

var keys = Object.keys(obj);
for (var i = 0; i < keys.length; i++) {
  var key = keys[i];
  var value = obj[key];
  console.log(key, value);
}
```

### Array Methods with Arrow Functions

```javascript
// ES6 (PROHIBITED)
items.forEach(item => console.log(item));
items.map(item => item.id);
items.filter(item => item.active);
items.find(item => item.id === targetId);
items.some(item => item.valid);
items.every(item => item.complete);
items.reduce((sum, item) => sum + item.price, 0);

// ES5 (REQUIRED)
items.forEach(function(item) { console.log(item); });
items.map(function(item) { return item.id; });
items.filter(function(item) { return item.active; });
// find() is ES6 - use custom implementation
var found = null;
for (var i = 0; i < items.length; i++) {
  if (items[i].id === targetId) {
    found = items[i];
    break;
  }
}
items.some(function(item) { return item.valid; });
items.every(function(item) { return item.complete; });
items.reduce(function(sum, item) { return sum + item.price; }, 0);
```

## Classes → Constructor Functions

```javascript
// ES6 (PROHIBITED)
class EventTracker {
  constructor(name) {
    this.name = name;
    this.events = [];
  }

  track(event) {
    this.events.push(event);
  }

  static create(name) {
    return new EventTracker(name);
  }
}

// ES5 (REQUIRED)
function EventTracker(name) {
  this.name = name;
  this.events = [];
}

EventTracker.prototype.track = function(event) {
  this.events.push(event);
};

EventTracker.create = function(name) {
  return new EventTracker(name);
};
```

## Block-Scoped Function Declarations

```javascript
// ES6/Non-standard (PROHIBITED)
if (condition) {
  function myFunction() {
    return 'Hello';
  }
  myFunction();
}

for (var i = 0; i < 10; i++) {
  function helper() {
    console.log(i);
  }
}

// ES5 (REQUIRED)
if (condition) {
  var myFunction = function() {
    return 'Hello';
  };
  myFunction();
}

for (var i = 0; i < 10; i++) {
  var helper = function() {
    console.log(i);
  };
}
```

## Promises and Async/Await

**Note:** Promises and async/await are NOT supported in GTM Custom HTML. Use callbacks instead.

```javascript
// ES6+ (PROHIBITED)
async function fetchData() {
  const response = await fetch('/api/data');
  const data = await response.json();
  return data;
}

fetch('/api').then(res => res.json()).then(data => process(data));

// ES5 (REQUIRED) - Use callbacks or XMLHttpRequest
function fetchData(callback) {
  var xhr = new XMLHttpRequest();
  xhr.open('GET', '/api/data', true);
  xhr.onreadystatechange = function() {
    if (xhr.readyState === 4 && xhr.status === 200) {
      var data = JSON.parse(xhr.responseText);
      callback(null, data);
    }
  };
  xhr.onerror = function() {
    callback(new Error('Request failed'), null);
  };
  xhr.send();
}
```

## ES5 Utility Functions

Include these utility functions when needed:

```javascript
// Object.assign polyfill
function extend(target) {
  for (var i = 1; i < arguments.length; i++) {
    var source = arguments[i];
    if (source) {
      for (var key in source) {
        if (source.hasOwnProperty(key)) {
          target[key] = source[key];
        }
      }
    }
  }
  return target;
}

// Array.isArray polyfill (for very old browsers)
function isArray(obj) {
  return Object.prototype.toString.call(obj) === '[object Array]';
}

// Array.prototype.find polyfill
function find(arr, predicate) {
  for (var i = 0; i < arr.length; i++) {
    if (predicate(arr[i], i, arr)) {
      return arr[i];
    }
  }
  return undefined;
}

// Array.prototype.includes polyfill
function includes(arr, value) {
  for (var i = 0; i < arr.length; i++) {
    if (arr[i] === value) return true;
  }
  return false;
}

// String.prototype.includes polyfill
function stringIncludes(str, search) {
  return str.indexOf(search) !== -1;
}

// String.prototype.startsWith polyfill
function startsWith(str, search) {
  return str.indexOf(search) === 0;
}

// String.prototype.endsWith polyfill
function endsWith(str, search) {
  return str.indexOf(search) === str.length - search.length;
}
```

## Complete Prohibited Features List

| Feature | Introduced | Alternative |
|---------|------------|-------------|
| `const` / `let` | ES6 | `var` |
| Arrow functions | ES6 | `function()` |
| Template literals | ES6 | String concatenation |
| Destructuring | ES6 | Individual property access |
| Spread operator | ES6 | `concat()`, `apply()` |
| Rest parameters | ES6 | `arguments` object |
| Default parameters | ES6 | `\|\|` or ternary |
| for-of | ES6 | Traditional `for` loop |
| Object method shorthand | ES6 | Explicit `: function()` |
| Computed properties | ES6 | Bracket notation |
| Classes | ES6 | Constructor functions |
| Promises | ES6 | Callbacks |
| async/await | ES2017 | Callbacks/XHR |
| Symbol | ES6 | Not available |
| Map/Set | ES6 | Plain objects/arrays |
| Proxy/Reflect | ES6 | Not available |
| import/export | ES6 | Not available (use IIFE) |
| Block functions | Non-standard | Function expressions |
| Optional chaining `?.` | ES2020 | Manual null checks |
| Nullish coalescing `??` | ES2020 | `\|\|` with explicit checks |
