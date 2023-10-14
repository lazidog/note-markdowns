# JS

## 1. Scope

- block scope: const, let
- func scope: var
- global scope: variables which declared outside func, assiged without declared

**note**: all global variables can access by using object `global` or `globalThis`

```js
//example global scope

var b = 'b';
fun();
console.log(global.a, global.b, global.c);
console.log(a, b, c); // output: a b c
function fun() {
  a = 'a'; // a is assiged without declared
  global.c = 'c';
}
```

### 2. Hosting

- JavaScript only hoists declarations, not initializations.
- Variables defined with `let` and `const` are hoisted to the top of the block, but not initialized.
- Functions in particular are moved at the top of their scope.

### 3. Function, Array and Object

### 4. This keyword [link](https://jintechflow.wordpress.com/2020/09/27/dive-in-depth-into-javascript-functions/#What-is-a-JavaScript-Function)

### 5. Date

`Date` objects contain a Number that represents milliseconds since 1 January 1970 UTC

#### Parameters

There are five basic forms for the `Date()` constructor:

- No parameters

the newly-created Date object represents the current date and time

- Time value or timestamp number

An integer value representing the number of milliseconds since January 1, 1970, 00:00:00 UTC

- Date string

> **always make sure that the input conforms to the ISO 8601 format (YYYY-MM-DDTHH:mm:ss.sssZ)** > **Date-only strings (e.g. "1970-01-01") are treated as UTC, while date-time strings (e.g. "1970-01-01T12:00") are treated as local.**

- Date Object

This effectively makes a copy of the existing Date object with the same date and time

- Individual date and time component values

#### Return value

Calling `new Date()` returns a Date object. If called with an invalid date string, or if the date to be constructed will have a UNIX timestamp less than -8,640,000,000,000,000 or greater than 8,640,000,000,000,000 (100,000,000 days) milliseconds, it returns a Date object whose toString() method returns the literal string Invalid Date.

> Calling the `Date()` function (without the `new` keyword) returns a string representation of the current date and time, exactly as new Date().toString() does. Any arguments given in a Date() function call (without the new keyword) are ignored; regardless of whether it's called with an invalid date string — or even called with any arbitrary object or other primitive as an argument — it always returns a string representation of the current date and time.

#### Passing a non-Date, non-string, non-number value

**If the Date() constructor is called with one parameter which is not a Date instance, it will be coerced to a primitive.**

`undefined` is already a primitive but not a string, so it will be coerced to a number, which is NaN and therefore not a valid timestamp. On the other hand, null will be coerced to 0.

```js
console.log(new Date(undefined)); // Invalid Date
console.log(new Date(null)); // 1970-01-01T00:00:00.000Z
```

## AIRBNB STYLE GUILD

1. **primitive type working directly on its value, complex type (array,..) working on its reference**

```js
const foo = [1, 2];
const bar = foo;

bar[0] = 9;
// value on its ref changed
console.log(foo[0], bar[0]); // => 9, 9
```

1. **prefer `const`, `let` than `var`**

   - **why?**

     `var` is func scope

     ```js
     {
       let a = 1;
       const b = 1;
       var c = 1;
     }
     console.log(a); // ReferenceError
     console.log(b); // ReferenceError
     console.log(c); // Prints 1
     ```

     one more bug for beginer without eslint

     ```js
     function printMatrix(matrix) {
       for (var i = 0; i < matrix.length; i++) {
         var line = matrix[i];
         for (var i = 0; i < line.length; i++) {
           var element = line[i];
           console.log(element);
         }
       }
     }

     var matrix = [
       [1, 2, 3],
       [4, 5, 6],
     ];

     printMatrix(matrix); // result: 1,2,3
     ```

## ESLINT vs PRETTIER

Prettier is a famous "code formatter" which ensures that all outputted code conforms to a consistent style.

ESLint is a JavaScript linting utility which performs static analysis in order to find problematic patterns or code that doesn't adhere to certain style guidelines.

- **How to fix conflig**

  A good way to avoid this problem is using Prettier as a ESLint plugin with **“eslint-plugin-prettier”**

  ```js
  // file .eslintrc.js
  module.exports = {
    ...
    rules: {
      ‘prettier/prettier’: ‘error’,
    },
    plugins: [‘prettier’],
  };
  ```

  But it could be that some rules will conflict. To avoid this problem, you have to turns off all rules that are unnecessary or might conflict with Prettier with **"eslint-config-prettier"**

  ```js
  module.exports = {
    ...
    extends: ['airbnb-base', 'prettier'],
    ...
  };
  ```
