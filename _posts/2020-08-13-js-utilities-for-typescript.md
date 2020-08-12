---
title: "Utility Snippets for Type-checking JavaScript"
category: tech
tags:
  - typescript
  - jsdoc
  - javascript
---

# Utility Snippets for Type-checking JavaScript

I use JSDoc comments to type-check my JavaScript code. TypeScript [supports](https://www.typescriptlang.org/docs/handbook/jsdoc-supported-types.html) a number of JSDoc tags. This allows me to write type-safe JavaScript without writing TypeScript. Visual Studio Code immediately reports any mistakes, which makes writing properly typed code a breeze.

Why not just write TypeScript? There is a certain sense of "purity", in being able to write code that can be run immediately without preprocessing. Call be a hipster.

However, JSDoc type annotations are second-class citizens in the TypeScript world. Certain TypeScript features are difficult or downright impossible to represent in JSDoc. Over time, I have gathered some tricks to bypass these limitations.

Note: All examples in this post have been tested in VS Code 1.47.3 and TypeScript 3.9.7.

## Conditional Types

JSDoc uses the question mark (`?`) to denote _nullable_ types. Because of this, TypeScript fails to recognize [conditional type expressions](https://www.typescriptlang.org/docs/handbook/advanced-types.html#conditional-types).

For example, the following TypeScript expression:

```ts
type MyType<T> = T extends string ? T : never;
```

...when written using JSDoc comments, yields an error:

```js
/**
 * @template T
 * @typedef {T extends string ? T : never} MyType
 */

// TypeScript rejects the above with:
// test.js:3:33 - error TS1005: '?' expected.
```

This can be worked around by placing the question mark on a new line:

```js
/**
 * @template T
 * @typedef {T extends string
        ? T : never
    } MyType
 */
```

Not pretty, but it does the job.

This trick was [discovered](https://github.com/microsoft/TypeScript/issues/27424#issuecomment-664547549) by [@ExE-Boss](https://github.com/ExE-Boss).

Apparently, the TypeScript team has been aware of the [issue](https://github.com/microsoft/TypeScript/issues/37166) since March. A [fix](https://github.com/microsoft/TypeScript/pull/39123) has been already integrated and is expected to ship with TypeScript 4.0.

## Literal Types

TypeScript supports [const assertions](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-3-4.html#const-assertions) for literals, tuples, and objects:

<!-- prettier-ignore -->
```ts
let v1 = "Foo Bar";           // type:  string
let v2 = "Foo Bar" as const;  // type:  "Foo Bar"

let a1 = ["baz", 1];          // type:  (string | number)[]
let a2 = ["baz", 1] as const; // type:  readonly ["baz, 1]
```

However, there is no equivalent using JSDoc comments alone:

```js
// This looks like it should work, but doesn't
let v1 = /** @type {const} */ ("Foo Bar"); // TS error: Cannot find name 'const'.
```

You can explicitly declare the literal type or use a type cast:

<!-- prettier-ignore -->
```ts
// Type declaration
/** @type {"Foo Bar"} */
let v2 = "Foo Bar";                             // type:  "Foo Bar"

// Type casting
let v3 = /** @type {"Foo Bar"} */ ("Foo Bar");  // type:  "Foo Bar"
```

However, this forfeits automatic type inference and creates duplicate code.

A couple of helper functions can remedy this:

<!-- prettier-ignore -->
```js
/**
 * Helper function for creating number and string literal types.
 * @template {number | string} T
 * @param {T} value
 * @return {T}
 */
function _c(value) {
  return value;
}

/**
 * Helper function for creating tuple literal types.
 * @template {any[]} T
 * @param {T} values
 * @return {T}
 */
function _t(...values) {
  return values;
}

// For number and string literals
let v1 = _c("Foo Bar");         // type:  "Foo Bar"
// For tuples
let v2 = _t(_c("baz"), _c(12)); // type:  ["baz", 12]
```
