---
title: "TypeScript runtime validators"
category: tech
tags:
  - javascript
  - typescript
  - validation
---

**_Note: This article is a work in progress._**

<details markdown="1">
<summary>Table of Contents</summary>
* TOC
{:toc}
</details>

## Disclaimer: What am I looking for?

I want to validate arbitrary JavaScript values--usually returned by `JSON.parse()`.

- I need to specify my own schema.
- A successfully validated value should be given a TypeScript type that matches the schema.
- No compile-time code generation. This excludes solutions such as [Typia](https://github.com/samchon/typia).
  - These solutions rely on a specific transpiler, such as the TypeScript compiler or Babel. This makes it difficult to use them with other transpilers like esbuild.

## Comparison of Validators

<!-- prettier-ignore-start -->

{: .large-table }
|  | [Ajv] | [Joi] | [Runtypes] | [Superstruct] | [TypeBox] | [Yup] | [Zod] |
| Version | ![][ajv-version] | ![][joi-version] | ![][runtypes-version] | ![][superstruct-version] | ![][typebox-version] | ![][yup-version] | ![][zod-version] |
| Downloads | ![][ajv-downloads] | ![][joi-downloads] | ![][runtypes-downloads] | ![][superstruct-downloads] | ![][typebox-downloads] | ![][yup-downloads] | ![][zod-downloads] |
| Stars | ![][ajv-stars] | ![][joi-stars] | ![][runtypes-stars] | ![][superstruct-stars] | ![][typebox-stars] | ![][yup-stars] | ![][zod-stars] |
| Minified Bundle Size | ![][ajv-size] | ![][joi-size] | ![][runtypes-size] | ![][superstruct-size] | ![][typebox-size][^note-typebox-size] | ![][yup-size] | ![][zod-size] |
| Last Commit | ![][ajv-lastcommit] | ![][joi-lastcommit] | ![][runtypes-lastcommit] | ![][superstruct-lastcommit] | ![][typebox-lastcommit] | ![][yup-lastcommit] | ![][zod-lastcommit] |
| Open Issues | ![][ajv-issues] | ![][joi-issues] | ![][runtypes-issues] | ![][superstruct-issues] | ![][typebox-issues] | ![][yup-issues] | ![][zod-issues] |
| Open PRs | ![][ajv-prs] | ![][joi-prs] | ![][runtypes-prs] | ![][superstruct-prs] | ![][typebox-prs] | ![][yup-prs] | ![][zod-prs] |

<!-- prettier-ignore-end -->

[ajv]: https://github.com/ajv-validator/ajv
[ajv-version]: https://badgen.net/npm/v/ajv?label=
[ajv-downloads]: https://img.shields.io/npm/dw/ajv?label=
[ajv-stars]: https://img.shields.io/github/stars/ajv-validator/ajv?logo=github&label=
[ajv-size]: https://img.shields.io/bundlephobia/min/ajv?label=
[ajv-issues]: https://img.shields.io/github/issues/ajv-validator/ajv?label=
[ajv-lastcommit]: https://img.shields.io/github/last-commit/ajv-validator/ajv?label=
[ajv-prs]: https://img.shields.io/github/issues-pr/ajv-validator/ajv?label=
[joi]: https://github.com/hapijs/joi
[joi-version]: https://badgen.net/npm/v/joi?label=
[joi-downloads]: https://img.shields.io/npm/dw/joi?label=
[joi-stars]: https://img.shields.io/github/stars/hapijs/joi?logo=github&label=
[joi-size]: https://img.shields.io/bundlephobia/min/joi?label=
[joi-issues]: https://img.shields.io/github/issues/hapijs/joi?label=
[joi-lastcommit]: https://img.shields.io/github/last-commit/hapijs/joi?label=
[joi-prs]: https://img.shields.io/github/issues-pr/hapijs/joi?label=
[runtypes]: https://github.com/pelotom/runtypes
[runtypes-version]: https://badgen.net/npm/v/runtypes?label=
[runtypes-downloads]: https://img.shields.io/npm/dw/runtypes?label=
[runtypes-stars]: https://img.shields.io/github/stars/pelotom/runtypes?logo=github&label=
[runtypes-size]: https://img.shields.io/bundlephobia/min/runtypes?label=
[runtypes-issues]: https://img.shields.io/github/issues/pelotom/runtypes?label=
[runtypes-lastcommit]: https://img.shields.io/github/last-commit/pelotom/runtypes?label=
[runtypes-prs]: https://img.shields.io/github/issues-pr/pelotom/runtypes?label=
[superstruct]: https://github.com/ianstormtaylor/superstruct
[superstruct-version]: https://badgen.net/npm/v/superstruct?label=
[superstruct-downloads]: https://img.shields.io/npm/dw/superstruct?label=
[superstruct-stars]: https://img.shields.io/github/stars/ianstormtaylor/superstruct?logo=github&label=
[superstruct-size]: https://img.shields.io/bundlephobia/min/superstruct?label=
[superstruct-issues]: https://img.shields.io/github/issues/ianstormtaylor/superstruct?label=
[superstruct-lastcommit]: https://img.shields.io/github/last-commit/ianstormtaylor/superstruct?label=
[superstruct-prs]: https://img.shields.io/github/issues-pr/ianstormtaylor/superstruct?label=
[typebox]: https://github.com/sinclairzx81/typebox
[typebox-version]: https://badgen.net/npm/v/@sinclair/typebox?label=
[typebox-downloads]: https://img.shields.io/npm/dw/@sinclair/typebox?label=
[typebox-stars]: https://img.shields.io/github/stars/sinclairzx81/typebox?logo=github&label=
[typebox-size]: https://img.shields.io/bundlephobia/min/@sinclair/typebox?label=
[typebox-issues]: https://img.shields.io/github/issues/sinclairzx81/typebox?label=
[typebox-lastcommit]: https://img.shields.io/github/last-commit/sinclairzx81/typebox?label=
[typebox-prs]: https://img.shields.io/github/issues-pr/sinclairzx81/typebox?label=
[yup]: https://github.com/jquense/yup
[yup-version]: https://badgen.net/npm/v/yup?label=
[yup-downloads]: https://img.shields.io/npm/dw/yup?label=
[yup-stars]: https://img.shields.io/github/stars/jquense/yup?logo=github&label=
[yup-size]: https://img.shields.io/bundlephobia/min/yup?label=
[yup-issues]: https://img.shields.io/github/issues/jquense/yup?label=
[yup-lastcommit]: https://img.shields.io/github/last-commit/jquense/yup?label=
[yup-prs]: https://img.shields.io/github/issues-pr/jquense/yup?label=
[zod]: https://github.com/colinhacks/zod
[zod-version]: https://badgen.net/npm/v/zod?label=
[zod-downloads]: https://img.shields.io/npm/dw/zod?label=
[zod-stars]: https://img.shields.io/github/stars/colinhacks/zod?logo=github&label=
[zod-size]: https://img.shields.io/bundlephobia/min/zod?label=
[zod-issues]: https://img.shields.io/github/issues/colinhacks/zod?label=
[zod-lastcommit]: https://img.shields.io/github/last-commit/colinhacks/zod?label=
[zod-prs]: https://img.shields.io/github/issues-pr/colinhacks/zod?label=

[^note-typebox-size]: This stat is misleading because `@sinclair/typebox` exports the built-in validator ("TypeCompiler") using a separate suffix. Testing indicates that, as of v0.25.16, adding the TypeCompiler increases the bundle size to ~40KB.

## External Links

- [moltar's comparison of TypeScript runtime type validation tools](https://moltar.github.io/typescript-runtime-type-benchmarks/)
