## Template string

### Tagged template

A tagged template is a function call where arguments of the call are dervied from a template literal.

```js
function tag(strings, nameExpr) {
  const greet = strings[0];

  if (nameExpr === "Peter") {
    return `${greet}${nameExpr}`;
  } else if (nameExpr === "Sam") {
    return ["Bruh"]; // return anything, doesn't need to be string
  }

  throw new Error("invalid name"); // throw an error, too
}

const name = "Peter";
const output = tag`hello world, ${name}`;
```

### Template string vs string

Besides the return value, there are a lot places in which string literal cannot be replaced 1-1 by template string literal:

- [Class property name](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#prod-PropertyName):
  ```js
  class Foo {
  	"baz": 100			// ok
  }
  // ------
  class Foo {
  	`baz`: 100			// error
  	[`baz`]: 100		// ok
  }
  ```
- [Top level import/export](https://tc39.es/ecma262/multipage/ecmascript-language-scripts-and-modules.html#prod-FromClause)
  ```js
  import { baz } from "foo"			// ok
  export { baz } from "foo"			// ok
  // ------
  import { bar } from `foo`			// error
  import { bar } from [`foo`]		// error
  export { baz } from `foo`			// error
  export { baz } from [`foo`]		// error
  ```
- [Object literal property name](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#prod-PropertyName)
  ```js
  const foo = { "baz": 100 }		// ok
  // ------
  const foo = { `baz`: 100 } 		// error
  const foo = { [`baz`]: 100 }	// ok
  ```
- [Object destructuring](https://tc39.es/ecma262/multipage/ecmascript-language-expressions.html#prod-AssignmentProperty)
  ```js
  const { "a": alias } = { a: 10} 		// ok
  // -----
  const { `a`: alias } = { a: 10} 		// error
  const { [`a`]: alias } = { a: 10} 	// ok
  ```
