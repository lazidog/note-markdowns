# Why Lodash?

- **Some func support for collections (Array|Object)**

Built-in array and object

```js
arr.forEach(e => {
  console.log(e);
});
Object.keys(obj).forEach(key => {
  console.log(obj[key]);
});
```

Lodash

```js
_.forEach(arr, e => console.log(e));
_.forEach(obj, (val, key) => console.log(key, val));
```

- **Iteratee / Predicate Shorthands and Against undefined values**

Built-in array and object

```js
const arr = [{ n: [0, 1] }, { n: [] }];
arr.forEach(e => {
  console.log(e);
}); // => { n: [0, 1] }, { n: [] }
arr.map(e => e.n); // => [[0, 1], []]
arr.map(e => e.n[0]); // =>
```

Lodash

```js
const arr = [{ n: [0, 1] }, { n: [] }];
_.map(arr, 'n'); // same as _.map(arr, _.property('n'))
_.map(arr, _.property('n[0]')); // => [ 0, undefined ]
_.forEach(arr, e => console.log(e));
_.forEach(obj, (val, key) => console.log(key, val));
```

- **Chaining**

Built-in array and object

```js
let arr = [1, 2, 3, 4, 5, '6', '7'];
arr = arr.map(e => (typeof e !== 'number' ? Number.parseInt(e, 10) : e));
let sum = 0;
arr.forEach((e, i) => {
  if (i % 2 === 0) sum += e;
});
// => sum = 1 + 3 + 5 + 7 = 16
```

Lodash

```js
const sum = _.chain([1, 2, 3, 4, 5, '6', '7'])
  .map(e => (!_.isNumber(e) ? _.parseInt(e) : e))
  .filter((e, i) => i % 2 === 0)
  .sum()
  .value(); // => 1 + 3 + 5 + 7 = 16
```

## Functions

### 1. chunk

**`Group` elements of an array with the length of size**

lodash: `_.chunk(array, [size=1])`

```js
_.chunk(['a', 'b', 'c', 'd', 'e'], 2);
// => [['a', 'b'], ['c', 'd'], ['e']]
```

### 2. concat

**`Merge` 2 or more arrays and/or values**

lodash: `_.concat(array, [values=....val])`

array: `.concat([values=....val])`

```js
_.concat(array, 2, [3], [[4]]);
// => [1, 2, 3, [4]]

const obj1 = { 0: 1, 1: 2, 2: 3, length: 3 };
const obj2 = { 0: 1, 1: 2, 2: 3, length: 3, [Symbol.isConcatSpreadable]: true };
console.log([0].concat(obj1, obj2));
// [ 0, { '0': 1, '1': 2, '2': 3, length: 3 }, 1, 2, 3 ]
```

> **note:** `concat` does not treat all array-like objects as arrays by default — only if **`Symbol.isConcatSpreadable`** is set to true.

### 3. fill

**`Fills` elements of array with value from start up to, but not including**

lodash: `_.fill(array, value, [start=0], [end=array.length])`

array: `.fill(value, [start=0], [end=array.length])`

> **note:** **Array.prototype.fill()** returns the modified array

```js
var array = [1, 2, 3];

_.fill(array, 'a');
// => ['a', 'a', 'a']

_.fill(Array(3), 2);
// => [2, 2, 2]

[4, 6, 8, 10].fill('*', 1, 3);
// => [4, '*', '*', 10]
```

### 4. findIndex

\_Return the index of first satisfied element, otherwise -1

lodash: `_.findIndex(array, [predicate=_.identity], [fromIndex=0])`

array: `.findIndex(function(element, index, array) { /* … */ })`

> **note:** Built-in Array just receive function as its params
>
> > predicate param in **`_.findIndex`** doesnt work with primitive var, use [`.indexOf()`](https://lodash.com/docs/4.17.15#indexOf) instead

```js
var users = [
  { user: 'barney', active: false },
  { user: 'pebbles', active: true },
];

_.findIndex(users, function (o) {
  return o.user == 'barney';
});
// => 0

// Array.prototype.findIndex() cannot use like these example
// The `_.matches`
_.findIndex(users, { user: 'fred', active: false });
// => 1

// The `_.matchesProperty`
_.findIndex(users, ['active', false]);
// => 0

// The `_.property`
_.findIndex(users, 'active');
// => 2
```

### 5. flat

**`Flatten` array up to depth times**

lodash: `_.flattenDepth(array, [depth=1])`

array: `.flat([depth=1])`

> **note:** Also **`flatten(array)`** flatten with single level, **`flattenDeep(array)`** flatten with max level

```js
var array = [1, [2, [3, [4]], 5]];

_.flattenDepth(array, 1);
// => [1, 2, [3, [4]], 5]
```

### 6. intersection

**Retrun an `array` of values included in all given arrays**

lodash: `_.intersection([arrays])`

```js
_.intersection([2, 1], [2, 3, 1], [2, 3, 1]);
// => [ 2, 1 ]
```

lodash: `_.intersectionWith([arrays], [comparator])`

```js
_.intersectionWith(
  [
    { x: 1, y: 2 },
    { x: 2, y: 1 },
  ],
  [
    { x: 1, y: 1 },
    { x: 1, y: 2 },
  ],
  _.isEqual
);
// => [{ 'x': 1, 'y': 2 }]
```

### 7. join

**Return a string by concatenating(nối) all of the elements of an array (or `array-like object`), separated by specified separator string**

lodash: `_.join(array, [separator=','])`

array: `.join([separator=','])`

```js
_.join(['a', 'b', 'c'], '~');
// => 'a~b~c'
```

### 8. pull

**Removes all given values from array.**

- `_.pull(array, [values])`

```js
_.pull(['a', 'b', 'c', 'a', 'b', 'c'], 'a', 'c');
// => ['b', 'b']
```

- `_.pullAll(array, values)`

```js
_.pullAll(['a', 'b', 'c', 'a', 'b', 'c'], ['a', 'c']);
// => ['b', 'b']
```

- `_.pullAllBy(array, values, [iteratee=_.identity])`

```js
_.pullAllBy(
  [{ x: 1 }, { x: 2 }, { x: 3 }, { x: 1 }],
  [{ x: 1 }, { x: 3 }],
  'x'
);
// => [{ 'x': 2 }]
```

- `_.pullAllWith(array, values, [comparator])`

```js
_.pullAllWith(
  [
    { x: 1, y: 2 },
    { x: 3, y: 4 },
    { x: 5, y: 6 },
  ],
  [{ x: 3, y: 4 }],
  _.isEqual
);
// => [{ 'x': 1, 'y': 2 }, { 'x': 5, 'y': 6 }]
```

- `_.pullAt(array, [indexes])`

> **note:** return new array of removed elements

```js
var array = ['a', 'b', 'c', 'd'];
var pulled = _.pullAt(array, [1, 3]);

// => array = ['a', 'c']
// => pulled = ['b', 'd']
```

### 9. uniq

**Return an array of `unique` value (the first occurrence)**

- `_.uniqBy` accept iteratee like `_.identity`, `_.uniqWith` accept comparator like \_.isEqual
  . So that, if u want to compare object u need to use uniqWith

```js
_.uniqWith(
  [
    { x: 1, y: 2 },
    { x: 2, y: 1 },
    { x: 1, y: 2 },
  ],
  _.isEqual
);
// => [ { x: 1, y: 2 }, { x: 2, y: 1 } ]
_.uniqBy(
  [
    { x: 1, y: 2 },
    { x: 2, y: 1 },
    { x: 1, y: 2 },
  ],
  _.identity
);
// => [ { x: 1, y: 2 }, { x: 2, y: 1 }, { x: 1, y: 2 } ]
```

- `_.uniq(array)`

```js
_.uniq([2, 1, 2]);
// => [2, 1]
```

- `_.uniqBy(array, [iteratee=_.identity])`

````js
_.uniqBy([2.1, 1.2, 2.3], Math.floor);
// => [2.1, 1.2]
```js
- `_.uniqWith(array, [comparator])`
````

### 10. countBy

**Return an object { `key`: value, `value`: number of times the key was returned by iteratee }**

lodash: `_.countBy(collection, [iteratee=_.identity])`

```js
_.countBy([6.1, 4.2, 6.3], Math.floor);
// => { '4': 1, '6': 2 }

// The `_.property` iteratee shorthand.
_.countBy(['one', 'two', 'three'], 'length');
// => { '3': 2, '5': 1 }
```

### 11. every

**Check if predicate return `true` for all elements**

lodash: `_.every(collection, [predicate=_.identity])`

```js
_.every([true, 1, null, 'yes'], Boolean);
// => false

var users = [
  { user: 'barney', age: 36, active: false },
  { user: 'fred', age: 40, active: false },
];

// The `_.matches` iteratee shorthand.
_.every(users, { user: 'barney', active: false });
// => false

// The `_.matchesProperty` iteratee shorthand.
_.every(users, ['active', false]);
// => true

// The `_.property` iteratee shorthand.
_.every(users, 'active');
// => false
```

### 12. groupBy

**Like `countBy` but value of each key is an array of elements represent for that key**

```js
_.groupBy([6.1, 4.2, 6.3], Math.floor);
// => { '4': [4.2], '6': [6.1, 6.3] }

// The `_.property` iteratee shorthand.
_.groupBy(['one', 'two', 'three'], 'length');
// => { '3': ['one', 'two'], '5': ['three'] }
```

### 13. `_.tap` vs `_.thru`

- `_.tap` can modify value directly and dont have return

- `_.thru` like `tap` but it return value

```js
const arr = [1, 2, 3];
_.chain(arr)
  .thru(value => [...value])
  .tap(value => value.pop())
  .value(); // => [1,2]
console.log(arr); // => [1,2,3] arr not modified
_.chain(arr)
  .tap(value => value.pop())
  .value(); // => [1,2]
console.log(arr); // => [1,2]
```

### 14. `_.forEach` vs `_.forIn`

Objects with a "length" property are iterated like arrays (array-like object)

- `_.forEach` treat collections as array

- `_.forIn` treat collections as object

```js
_.forEach({ 0: 1, 1: { x: 1 }, 2: 'asdas', length: 2 }, (value, index, arr) => {
  console.log(value, index);
});
// =>   1 0
        { x: 1 } 1

_.forIn({ 0: 1, 1: { x: 1 }, 2: 'asdas', length: 2 }, (value, key, obj) => {
  console.log(value, key);
});
// =>   1 0
        { x: 1 } 1
        asdas 2
        2 length
```

### 15. `_.remove`, `_.pull` and `_.without`

- `_.remove` and `_.pull` will mutate the array, `_.without` will not
- `_.pull` and `_.without` remove by values, `_.remove` remove by predicate
- `_.pull` and `_.without` return ok values, `_.remove` return removed value

```js
let arr = [1, 2, 3, 4];
console.log(_.without(arr, 1, 2)); // => [3, 4]
arr = [1, 2, 3, 4];
console.log(_.remove(arr, val => [1, 2].includes(val))); // => [1, 2]
arr = [1, 2, 3, 4];
console.log(_.pull(arr, 1, 2)); // => [3, 4]
```

### 16. `_.eq`, `_.isEqual`, `_.isEqualwith`

- `_.eq` as same as `===`
- `_.isEqual` ex deep comparison

```js
const obj1 = { a: 1 };
const obj2 = { a: 1 };
console.log(
  _.isEqual(obj1, obj2), // => true
  _.eq(obj1, obj2), // => false
  obj1 === obj2, // => false
  _.isEqualWith(
    { a: 1 },
    { a: '1' },
    (o1, o2) => _.toString(o1.a) === _.toString(o2.a)
  ) // => true
);
```

### 17. convertion

- `_toPlainObject` flatten all string keyed properties

```js
function Foo() {
  this.b = 2;
}
Foo.prototype.c = 3;
const foo = new Foo();
console.log(foo); // => Foo: { b : 2 }
console.log(_.toPlainObject(foo)); // => { b: 2, c: 3 }
```

- `_toArray` convert to array

```js
console.log(
  _.toArray({ 0: { a: 1 }, 1: 2, length: 2 }), // => [ { a: 1 }, 2 ]
  _.toArray({ a: { a: 1 }, b: 2, c: [] }), // => [ { a: 1 }, 2, [] ]
  _.toArray('abc'); // => ['a', 'b', 'c']
);
```

### 18. group and ungroup

- `_.chunk` group element with size

```js
_.chunk(['a', 'b', 'c', 'd', 'e'], 2);
// => [['a', 'b'], ['c', 'd'], ['e']]
```

- `_.zip` group element with order

```js
_.zip(['a', 'b'], [1, 2], [true, false]);
// => [['a', 'b'], ['c', 'd'], ['e']]
```
