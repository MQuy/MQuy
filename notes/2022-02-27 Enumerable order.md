## Enumerable order

Depending on a spec version and a vendor (browers), the behavior can be changed. From ES3-ES5, enumerating properties in the way they are defined seems to be de factor standard.

### ECMAScript 2022

[`OwnPropertyKeys`](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys) is sorted by:

- Number: ascending numeric index order (converted to String later).
- String: ascending chronological order of property creation.
- Symbol: ascending chronological order of property creation

#### For-In

[`for-in`](https://tc39.es/ecma262/#sec-runtime-semantics-forinofheadevaluation) uses [`EnumerateObjectProperties`](https://tc39.es/ecma262/#sec-enumerate-object-properties). How it works:

- for each key K in [`OwnPropertyKeys`](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys):
  - ignore if K is not a string.
  - mark K as visited.
  - return K if K is enumerable.
- do the same for the object's prototype except that ignoring a key that is already visited.

```js
const base = {
  knock: 11,
  bonk: 12,
  30: 13,
};

const obj = {
  tick: 21,
  tock: 22,
  [Symbol("bruh")]: 24,
  10: 23,
};
Object.defineProperty(obj, "bonk", { enumerable: false }); // obj.bonk is defined causes base.bonk is not selected despite obj.bonk's enumerable is false.
Object.setPrototypeOf(obj, base);

for (let key in obj) {
  console.log(key, typeof key);
}
// 10, tick, tock, 30, knock
```

✍️ using [`EnumerateObjectProperties`](https://tc39.es/ecma262/#sec-enumerate-object-properties) means that

> The mechanics and order of enumerating the properties is not specified

#### Object.keys

[`Object.keys`](https://tc39.es/ecma262/#sec-enumerableownpropertynames) uses [`EnumerableOwnPropertyNames`](https://tc39.es/ecma262/#sec-enumerableownpropertynames), how it works:

- for each key K in [`OwnPropertyKeys`](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys):
  - ignore if K is not string and not enumerable.
  - add K into returned properties.

```js
const obj = {
  tick: 21,
  tock: 22,
  [Symbol("bruh")]: 24,
  10: 23,
};
Object.defineProperty(obj, "bonk", { enumerable: false });

console.log(Object.keys(obj)); // ["10", "tick", "tock"]
```

The other methods uses [`EnumerableOwnPropertyNames`](https://tc39.es/ecma262/#sec-enumerableownpropertynames):

- [`Object.values`](https://tc39.es/ecma262/#sec-object.values)
- [`Object.entries`](https://tc39.es/ecma262/#sec-object.entries)
- [`JSON.parse`](https://tc39.es/ecma262/#sec-json.parse) via [`InternalizeJSONProperty`](https://tc39.es/ecma262/#sec-internalizejsonproperty)
- [`JSON.stringify`](https://tc39.es/ecma262/#sec-json.stringify) via [`SerializeJSONObject`](https://tc39.es/ecma262/#sec-serializejsonobject)

✍️ using [`EnumerableOwnPropertyNames`](https://tc39.es/ecma262/#sec-enumerableownpropertynames) means their results are in an specified order.

#### Reflect.ownKeys

[`Reflect.ownKeys`](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys) works smiliar to `Object.keys` except it contains `Symbol`.

```js
const obj = {
  tick: 21,
  tock: 22,
  [Symbol("bruh")]: 24,
  10: 23,
};
Object.defineProperty(obj, "bonk", { enumerable: false });

console.log(Reflect.ownKeys(obj)); // ["10", "tick", "tock", Symbol(bruh)]
```

The other methods use [`[[OwnPropertyKeys]]`](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys):

- [`Object.getOwnPropertyNames`](https://tc39.es/ecma262/#sec-object.getownpropertynames) (via [`GetOwnPropertyKeys`](https://tc39.es/ecma262/#sec-getownpropertykeys))
- [`Object.getOwnPropertySymbols`](https://tc39.es/ecma262/#sec-object.getownpropertysymbols) (via [`GetOwnPropertyKeys`](https://tc39.es/ecma262/#sec-getownpropertykeys))
- [`Object.assign`](https://tc39.es/ecma262/#sec-object.assign), [`Object.create`](https://tc39.es/ecma262/#sec-object.create) (via [`ObjectDefineProperties`](https://tc39.es/ecma262/#sec-objectdefineproperties))
- [`Object.defineProperties`](https://tc39.es/ecma262/#sec-object.defineproperties) via [`ObjectDefineProperties`](https://tc39.es/ecma262/#sec-objectdefineproperties)
- [`Object.getOwnPropertyDescriptors`](https://tc39.es/ecma262/#sec-object.getownpropertydescriptors)
- [`Object.freeze`](https://tc39.es/ecma262/#sec-object.freeze) (via [`SetIntegrityLevel`](https://tc39.es/ecma262/#sec-setintegritylevel))
- [`Object.seal`](https://tc39.es/ecma262/#sec-object.seal) (via [`SetIntegrityLevel`](https://tc39.es/ecma262/#sec-setintegritylevel))
- [`Object.isFrozen`](https://tc39.es/ecma262/#sec-object.isfrozen) (via [`TestIntegrityLevel`](https://tc39.es/ecma262/#sec-testintegritylevel))
- [`Object.isSealed`](https://tc39.es/ecma262/#sec-object.issealed) (via [`TestIntegrityLevel`](https://tc39.es/ecma262/#sec-testintegritylevel))
- [`object spread`](https://tc39.es/ecma262/#sec-object-initializer-runtime-semantics-propertydefinitionevaluation) (via [`CopyDataProperties`](https://tc39.es/ecma262/#sec-copydataproperties))
- object rest in both [`assignment`](https://tc39.es/ecma262/#sec-runtime-semantics-restdestructuringassignmentevaluation) and [`binding`](https://tc39.es/ecma262/#sec-destructuring-binding-patterns-runtime-semantics-restbindinginitialization) position (via [`CopyDataProperties`](https://tc39.es/ecma262/#sec-copydataproperties))

✍ using [`[[OwnPropertyKeys]]`](https://tc39.es/ecma262/#sec-ordinary-object-internal-methods-and-internal-slots-ownpropertykeys) means their results are in an specified order.

### ECMAScript 2015

`for-in` outputs an unspecified order. If an implementation defines a specific order for enumeration `for-in`, the same order must be use for:

- [`Object.keys`](https://262.ecma-international.org/6.0/#sec-object.keys)
- [`JSON.parse`](https://262.ecma-international.org/6.0/#sec-json.parse) via [`InternalizeJSONProperty`](https://262.ecma-international.org/6.0/#sec-object.keys)
- [`JSON.stringify`](https://262.ecma-international.org/6.0/#sec-json.stringify) via [`SerializeJSONObject`](https://262.ecma-international.org/6.0/#sec-serializejsonobject)

### Map in Golang

The order of map iteration is unspecified. In practice, the order is random, varying from one execution to the next (force program to not rely on the order).

### References

- [Specifing for-in enumeration order](https://github.com/tc39/proposal-for-in-order)
- [The Go Programming Language](https://learning.oreilly.com/library/view/the-go-programming/9780134190570/)
- [Javascript in Chrome](https://johnresig.com/blog/javascript-in-chrome/)
