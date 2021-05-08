---
title: "JavaScript command-line parsing libraries"
category: tech
tags:
  - javascript
  - cli
---

There are many JavaScript libraries for parsing command line arguments. These can be categorized broadly into two groups: Batteries-included and Minimal.

## Disclaimer: What am I looking for?

I need an command line parser that can run in [Mozilla Rhino](https://github.com/mozilla/rhino). This puts some restrictions on my choices:

- No Node.js builtins: Since Rhino does not have Node.js modules (e.g. [path](https://nodejs.org/docs/latest/api/path.html)), and it would be difficult to polyfill them or inject stubs during bundling.
- No `setTimeout()`, promises, or async/await: Since Rhino does not have any of these features, I have to avoid libraries that use these features.
- Convenience features (e.g. help message generation) would be nice.
- Small bundle size would be great (\< 10 kB)

Most people won't be burdened with these limitations. Nevertheless, the following tables may be useful for someone looking for an argument parsing tool.

## Batteries included

Several libraries provide many features out of the box: Help generation, error handling, subcommands, asynchronous execution, type checking, colored output, autocompletion, etc. They also provide declarative or [fluent](https://en.wikipedia.org/wiki/Fluent_interface) APIs for better developer experience. These are often recommended to anyone building a CLI application.

<!-- prettier-ignore-start -->

{: .large-table }
|  | [argparse] | [args] | [caporal][][^note-caporal] | [clap] | [commander] | [dashdash] | [meow] | [optimist] | [optionator] | [sade] | [yargs][][^note-yargs] |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Version | ![][argparse-version] | ![][args-version] | ![][caporal-old-version]<br>![][caporal-version] | ![][clap-version] | ![][commander-version] | ![][dashdash-version] | ![][meow-version] | ![][optimist-version] | ![][optionator-version] | ![][sade-version] | ![][yargs-version] |
| Downloads | ![][argparse-downloads] | ![][args-downloads] | ![][caporal-old-downloads]<br>![][caporal-downloads] | ![][clap-downloads] | ![][commander-downloads] | ![][dashdash-downloads] | ![][meow-downloads] | ![][optionator-downloads] | ![][sade-downloads] | ![][yargs-downloads] |
| Stars | ![][argparse-stars] | ![][args-stars] | ![][caporal-stars] | ![][clap-stars] | ![][commander-stars] | ![][dashdash-stars] | ![][meow-stars] | ![][optionator-stars] | ![][sade-stars] | ![][yargs-stars] |
| Minified Size | ![][argparse-size] | ![][args-size] | ![][caporal-size] | ![][clap-size] | ![][commander-size] | ![][dashdash-size] | ![][meow-size] | ![][optimist-size] | ![][optionator-size] | ![][sade-size] | ![][yargs-size] |
| First Commit | May 2012 | Apr 2012 | Feb 2017 | Feb 2014 | Aug 2011 | Feb 2013 | Jan 2013 | Dec 2010 | Nov 2013 | May 2017 | Nov 2013 |
| Last Commit | ![][argparse-last-commit] | ![][args-last-commit] | ![][caporal-last-commit] | ![][clap-last-commit] | ![][commander-last-commit] | ![][dashdash-last-commit] | ![][meow-last-commit] | ![][optimist-last-commit] | ![][optionator-last-commit] | ![][sade-last-commit] | ![][yargs-last-commit] |
| Open Issues | ![][argparse-issues] | ![][args-issues] | ![][caporal-issues] | ![][clap-issues] | ![][commander-issues] | ![][dashdash-issues] | ![][meow-issues] | ![][optimist-issues] | ![][optionator-issues] | ![][sade-issues] | ![][yargs-issues] |
| Open PRs | ![][argparse-pull-requests] | ![][args-pull-requests] | ![][caporal-pull-requests] | ![][clap-pull-requests] | ![][commander-pull-requests] | ![][dashdash-pull-requests] | ![][meow-pull-requests] | ![][optimist-pull-requests] | ![][optionator-pull-requests] | ![][sade-pull-requests] | ![][yargs-pull-requests] |
| Parser | (self)[^note-self] | [mri] | (self)[^note-self] | (self)[^note-self] | (self)[^note-self] | (self)[^note-self] | [yargs-parser] | [minimist] | (self)[^note-self] | [mri] | [yargs-parser] |
| [Dependency count](#dependency-count) | ![][depcount-argparse] | ![][depcount-args] | ![][depcount-caporal] | ![][depcount-clap] | ![][depcount-commander] | ![][depcount-dashdash] | ![][depcount-meow] | ![][depcount-optimist] | ![][depcount-optionator] | ![][depcount-sade] | ![][depcount-yargs] |
| [Node.js APIs used](#nodejs-apis-used) | `assert`, `fs`, `path`, `process`, `util` | `child_process`, `fs`, `path`, `process` | `events`, `fs`, `os`, `path`, `process`, `util` | `path`, `process` | `child_process`, `events`, `fs`, `path`, `process` | `fs`, `path`, `process`, `util` | `path`, `process`, `url` | `path`, `process` | `process` | `process` | - |
| [Async code](#async-code) | ? | ? | ? | ? | ? | ? | ? | ? | ? | ? | ⭕ |

<!-- prettier-ignore-end -->

[argparse]: https://github.com/nodeca/argparse
[argparse-version]: https://badgen.net/npm/v/argparse?label=
[argparse-downloads]: https://img.shields.io/npm/dw/argparse?label=
[argparse-stars]: https://img.shields.io/github/stars/nodeca/argparse?logo=github&label=
[argparse-size]: https://img.shields.io/bundlephobia/min/argparse?label=
[argparse-last-commit]: https://img.shields.io/github/last-commit/nodeca/argparse?label=
[argparse-issues]: https://img.shields.io/github/issues/nodeca/argparse?label=
[argparse-pull-requests]: https://img.shields.io/github/issues-pr/nodeca/argparse?label=
[args]: https://github.com/leo/args
[args-version]: https://badgen.net/npm/v/args?label=
[args-downloads]: https://img.shields.io/npm/dw/args?label=
[args-stars]: https://img.shields.io/github/stars/leo/args?logo=github&label=
[args-size]: https://img.shields.io/bundlephobia/min/args?label=
[args-last-commit]: https://img.shields.io/github/last-commit/leo/args?label=
[args-issues]: https://img.shields.io/github/issues/leo/args?label=
[args-pull-requests]: https://img.shields.io/github/issues-pr/leo/args?label=
[caporal]: https://github.com/mattallty/Caporal.js
[caporal-old-version]: https://badgen.net/npm/v/caporal?label=old
[caporal-version]: https://badgen.net/npm/v/@caporal/core?label=new
[caporal-old-downloads]: https://img.shields.io/npm/dw/caporal?label=old
[caporal-downloads]: https://img.shields.io/npm/dw/@caporal/core?label=new
[caporal-stars]: https://img.shields.io/github/stars/mattallty/Caporal.js?logo=github&label=
[caporal-size]: https://img.shields.io/bundlephobia/min/@caporal/core?label=
[caporal-last-commit]: https://img.shields.io/github/last-commit/mattallty/Caporal.js?label=
[caporal-issues]: https://img.shields.io/github/issues/mattallty/Caporal.js?label=
[caporal-pull-requests]: https://img.shields.io/github/issues-pr/mattallty/Caporal.js?label=
[clap]: https://github.com/lahmatiy/clap
[clap-version]: https://badgen.net/npm/v/clap?label=
[clap-downloads]: https://img.shields.io/npm/dw/clap?label=
[clap-stars]: https://img.shields.io/github/stars/lahmatiy/clap?logo=github&label=
[clap-size]: https://img.shields.io/bundlephobia/min/clap?label=
[clap-last-commit]: https://img.shields.io/github/last-commit/lahmatiy/clap?label=
[clap-issues]: https://img.shields.io/github/issues/lahmatiy/clap?label=
[clap-pull-requests]: https://img.shields.io/github/issues-pr/lahmatiy/clap?label=
[commander]: https://github.com/tj/commander.js
[commander-version]: https://badgen.net/npm/v/commander?label=
[commander-downloads]: https://img.shields.io/npm/dw/commander?label=
[commander-stars]: https://img.shields.io/github/stars/tj/commander.js?logo=github&label=
[commander-size]: https://img.shields.io/bundlephobia/min/commander?label=
[commander-last-commit]: https://img.shields.io/github/last-commit/tj/commander.js?label=
[commander-issues]: https://img.shields.io/github/issues/tj/commander.js?label=
[commander-pull-requests]: https://img.shields.io/github/issues-pr/tj/commander?label=
[dashdash]: https://github.com/trentm/node-dashdash
[dashdash-version]: https://badgen.net/npm/v/dashdash?label=
[dashdash-downloads]: https://img.shields.io/npm/dw/dashdash?label=
[dashdash-stars]: https://img.shields.io/github/stars/trentm/node-dashdash?logo=github&label=
[dashdash-size]: https://img.shields.io/bundlephobia/min/dashdash?label=
[dashdash-last-commit]: https://img.shields.io/github/last-commit/trentm/node-dashdash?label=
[dashdash-issues]: https://img.shields.io/github/issues/trentm/node-dashdash?label=
[dashdash-pull-requests]: https://img.shields.io/github/issues-pr/trentm/node-dashdash?label=
[meow]: https://github.com/sindresorhus/meow
[meow-version]: https://badgen.net/npm/v/meow?label=
[meow-downloads]: https://img.shields.io/npm/dw/meow?label=
[meow-stars]: https://img.shields.io/github/stars/sindresorhus/meow?logo=github&label=
[meow-size]: https://img.shields.io/bundlephobia/min/meow?label=
[meow-last-commit]: https://img.shields.io/github/last-commit/sindresorhus/meow?label=
[meow-issues]: https://img.shields.io/github/issues/sindresorhus/meow?label=
[meow-pull-requests]: https://img.shields.io/github/issues-pr/sindresorhus/meow?label=
[optimist]: https://github.com/substack/node-optimist
[optimist-version]: https://badgen.net/npm/v/optimist?label=
[optimist-downloads]: https://img.shields.io/npm/dw/optimist?label=
[optimist-stars]: https://img.shields.io/github/stars/substack/node-optimist?logo=github&label=
[optimist-size]: https://img.shields.io/bundlephobia/min/optimist?label=
[optimist-last-commit]: https://img.shields.io/github/last-commit/substack/node-optimist?label=
[optimist-issues]: https://img.shields.io/github/issues/substack/node-optimist?label=
[optimist-pull-requests]: https://img.shields.io/github/issues-pr/substack/node-optimist?label=
[optionator]: https://github.com/gkz/optionator
[optionator-version]: https://badgen.net/npm/v/optionator?label=
[optionator-downloads]: https://img.shields.io/npm/dw/optionator?label=
[optionator-stars]: https://img.shields.io/github/stars/gkz/optionator?logo=github&label=
[optionator-size]: https://img.shields.io/bundlephobia/min/optionator?label=
[optionator-last-commit]: https://img.shields.io/github/last-commit/gkz/optionator?label=
[optionator-issues]: https://img.shields.io/github/issues/gkz/optionator?label=
[optionator-pull-requests]: https://img.shields.io/github/issues-pr/gkz/optionator?label=
[sade]: https://github.com/lukeed/sade
[sade-version]: https://badgen.net/npm/v/sade?label=
[sade-downloads]: https://img.shields.io/npm/dw/sade?label=
[sade-stars]: https://img.shields.io/github/stars/lukeed/sade?logo=github&label=
[sade-size]: https://img.shields.io/bundlephobia/min/sade?label=
[sade-last-commit]: https://img.shields.io/github/last-commit/lukeed/sade?label=
[sade-issues]: https://img.shields.io/github/issues/lukeed/sade?label=
[sade-pull-requests]: https://img.shields.io/github/issues-pr/lukeed/sade?label=
[yargs]: https://github.com/yargs/yargs
[yargs-version]: https://badgen.net/npm/v/yargs?label=
[yargs-downloads]: https://img.shields.io/npm/dw/yargs?label=
[yargs-stars]: https://img.shields.io/github/stars/yargs/yargs?logo=github&label=
[yargs-size]: https://img.shields.io/bundlephobia/min/yargs?label=
[yargs-last-commit]: https://img.shields.io/github/last-commit/yargs/yargs?label=
[yargs-issues]: https://img.shields.io/github/issues/yargs/yargs?label=
[yargs-pull-requests]: https://img.shields.io/github/issues-pr/yargs/yargs?label=
[depcount-argparse]: https://badgen.net/bundlephobia/dependency-count/argparse?label=
[depcount-args]: https://badgen.net/bundlephobia/dependency-count/args?label=
[depcount-caporal]: https://badgen.net/bundlephobia/dependency-count/@caporal/core?label=
[depcount-clap]: https://badgen.net/bundlephobia/dependency-count/clap?label=
[depcount-commander]: https://badgen.net/bundlephobia/dependency-count/commander?label=
[depcount-dashdash]: https://badgen.net/bundlephobia/dependency-count/dashdash?label=
[depcount-meow]: https://badgen.net/bundlephobia/dependency-count/meow?label=
[depcount-optimist]: https://badgen.net/bundlephobia/dependency-count/optimist?label=
[depcount-optionator]: https://badgen.net/bundlephobia/dependency-count/optionator?label=
[depcount-sade]: https://badgen.net/bundlephobia/dependency-count/sade?label=
[depcount-yargs]: https://badgen.net/bundlephobia/dependency-count/yargs?label=

[^note-caporal]: Caporal v1 is released as the `caporal` package, while v2 is the `@caporal/core` package. As such, their download numbers are counted separately.
[^note-self]: These libraries parse options directly instead of relying on another package.
[^note-yargs]: I only looked at the browser-specific bundle of yargs. The CommonJS bundle uses several Node.js APIs and cannot be bundled for Rhino.

Unfortunately, almost all of them rely on Node.js builtins or use async code. They are also fairly large in terms of bundle size. This makes them unsuitable for my use case.

## Minimal argument parsers

These libraries focus on parsing an array of arguments and provide little else. They are sometimes used to power the batteries-included libraries.

<!-- prettier-ignore-start -->

{: .large-table }
|  | [arg] | [command-line-args] | [getopts] | [minimist] | [mri] | [yargs-parser] |
| --- | --- | --- | --- | --- | --- | --- |
| Version | ![][arg-version] | ![][command-line-args-version] | ![][getopts-version] | ![][minimist-version] | ![][mri-version] | ![][yargs-parser-version] |
| Downloads | ![][arg-downloads] | ![][command-line-args-downloads] | ![][getopts-downloads] | ![][minimist-downloads] | ![][mri-downloads] | ![][yargs-parser-downloads] |
| Stars | ![][arg-stars] | ![][command-line-args-stars] | ![][getopts-stars] | ![][minimist-stars] | ![][mri-stars] | ![][yargs-parser-stars] |
| Minified Size | ![][arg-size] | ![][command-line-args-size] | ![][getopts-size] | ![][minimist-size] | ![][mri-size] | ![][yargs-parser-size] |
| First Commit | Jun 2012 | May 2014 | May 2015 | Jun 2013 | Apr 2017 | Jan 2016 |
| Last Commit | ![][arg-last-commit] | ![][command-line-args-last-commit] | ![][getopts-last-commit] | ![][minimist-last-commit] | ![][mri-last-commit] | ![][yargs-parser-last-commit] |
| Open Issues | ![][arg-issues] | ![][command-line-args-issues] | ![][getopts-issues] | ![][minimist-issues] | ![][mri-issues] | ![][yargs-parser-issues] |
| Open PRs | ![][arg-pull-requests] | ![][command-line-args-pull-requests] | ![][getopts-pull-requests] | ![][minimist-pull-requests] | ![][mri-pull-requests] | ![][yargs-parser-pull-requests] |

<!-- prettier-ignore-end -->

[arg]: https://github.com/vercel/arg
[arg-version]: https://badgen.net/npm/v/arg?label=
[arg-downloads]: https://img.shields.io/npm/dw/arg?label=
[arg-stars]: https://img.shields.io/github/stars/vercel/arg?logo=github&label=
[arg-size]: https://img.shields.io/bundlephobia/min/arg?label=
[arg-last-commit]: https://img.shields.io/github/last-commit/vercel/arg?label=
[arg-issues]: https://img.shields.io/github/issues/vercel/arg?label=
[arg-pull-requests]: https://img.shields.io/github/issues-pr/vercel/arg?label=
[command-line-args]: https://github.com/75lb/command-line-args
[command-line-args-version]: https://badgen.net/npm/v/command-line-args?label=
[command-line-args-downloads]: https://img.shields.io/npm/dw/command-line-args?label=
[command-line-args-stars]: https://img.shields.io/github/stars/75lb/command-line-args?logo=github&label=
[command-line-args-size]: https://img.shields.io/bundlephobia/min/command-line-args?label=
[command-line-args-last-commit]: https://img.shields.io/github/last-commit/75lb/command-line-args?label=
[command-line-args-issues]: https://img.shields.io/github/issues/75lb/command-line-args?label=
[command-line-args-pull-requests]: https://img.shields.io/github/issues-pr/75lb/command-line-args?label=
[getopts]: https://github.com/jorgebucaran/getopts
[getopts-version]: https://badgen.net/npm/v/getopts?label=
[getopts-downloads]: https://img.shields.io/npm/dw/getopts?label=
[getopts-stars]: https://img.shields.io/github/stars/jorgebucaran/getopts?logo=github&label=
[getopts-size]: https://img.shields.io/bundlephobia/min/getopts?label=
[getopts-last-commit]: https://img.shields.io/github/last-commit/jorgebucaran/getopts?label=
[getopts-issues]: https://img.shields.io/github/issues/jorgebucaran/getopts?label=
[getopts-pull-requests]: https://img.shields.io/github/issues-pr/jorgebucaran/getopts?label=
[minimist]: https://github.com/substack/minimist
[minimist-version]: https://badgen.net/npm/v/minimist?label=
[minimist-downloads]: https://img.shields.io/npm/dw/minimist?label=
[minimist-stars]: https://img.shields.io/github/stars/substack/minimist?logo=github&label=
[minimist-size]: https://img.shields.io/bundlephobia/min/minimist?label=
[minimist-last-commit]: https://img.shields.io/github/last-commit/substack/minimist?label=
[minimist-issues]: https://img.shields.io/github/issues/substack/minimist?label=
[minimist-pull-requests]: https://img.shields.io/github/issues-pr/substack/minimist?label=
[mri]: https://github.com/lukeed/mri
[mri-version]: https://badgen.net/npm/v/mri?label=
[mri-downloads]: https://img.shields.io/npm/dw/mri?label=
[mri-stars]: https://img.shields.io/github/stars/lukeed/mri?logo=github&label=
[mri-size]: https://img.shields.io/bundlephobia/min/mri?label=
[mri-last-commit]: https://img.shields.io/github/last-commit/lukeed/mri?label=
[mri-issues]: https://img.shields.io/github/issues/lukeed/mri?label=
[mri-pull-requests]: https://img.shields.io/github/issues-pr/lukeed/mri?label=
[yargs-parser]: https://github.com/yargs/yargs-parser
[yargs-parser-version]: https://badgen.net/npm/v/yargs-parser?label=
[yargs-parser-downloads]: https://img.shields.io/npm/dw/yargs-parser?label=
[yargs-parser-stars]: https://img.shields.io/github/stars/yargs/yargs-parser?logo=github&label=
[yargs-parser-size]: https://img.shields.io/bundlephobia/min/yargs-parser?label=
[yargs-parser-last-commit]: https://img.shields.io/github/last-commit/yargs/yargs-parser?label=
[yargs-parser-issues]: https://img.shields.io/github/issues/yargs/yargs-parser?label=
[yargs-parser-pull-requests]: https://img.shields.io/github/issues-pr/yargs/yargs-parser?label=

### Comparison of minimal command-line argument parser libraries

Information in the following table describes the latest versions of each library as of 2021-05-07. Click on each criteria for an explanation.

Feature description:

- ❌ Unsupported[^note-unsupported]
- ✅ Supported
- ⚙ Can be configured

<!-- prettier-ignore-start -->

{: .large-table }
| Criteria | arg | command-line-args | getopts | minimist | mri | yargs-parser[^note-yargs-parser] |
| -------- | :-: | :---------------: | :-----: | :------: | :-: | :----------: |
| [Dependency count](#dependency-count) | ![][depcount-arg] | ![][depcount-command-line-args] | ![][depcount-getopts] | ![][depcount-minimist] | ![][depcount-mri] | ![][depcount-yargs-parser] |
| [Node.js APIs used](#nodejs-apis-used) | `process` | `process` | - | - | - | - |
| [Async code](#async-code) | - | - | - | - | - | - |
| [Accept single string](#accept-single-string) | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ |
| [Negative options](#negative-options) | ❌ | ❌ | ✅ | ✅ | ✅ | ✅⚙ |
| [Camelcase alias](#camelcase-alias) | ❌ | ✅⚙ | ❌ | ❌ | ❌ | ✅⚙ |
| [Count repeated options](#count-repeated-options) | ✅⚙ | ❌ | ❌ | ❌ | ❌ | ✅⚙ |
| [Stop at first non-option](#stop-at-first-non-option) | ✅⚙ | ✅⚙ | ✅⚙ | ✅⚙ | ❌ | ✅⚙ |
| [Discriminate tokens after `--`](#discriminate-tokens-after---) | ❌ | ❌ | ❌ | ✅⚙ | ❌ | ✅⚙ |
| [Dotted option to object](#dotted-option-to-object) | ❌ | ❌ | ❌ | ✅ | ❌ | ✅⚙ |
| [Custom conversion functions](#custom-conversion-functions) | ✅⚙ | ✅⚙ | ❌ | ❌ | ❌ | ✅⚙ |

<!-- prettier-ignore-end -->

[depcount-arg]: https://badgen.net/bundlephobia/dependency-count/arg?label=
[depcount-command-line-args]: https://badgen.net/bundlephobia/dependency-count/command-line-args?label=
[depcount-getopts]: https://badgen.net/bundlephobia/dependency-count/getopts?label=
[depcount-minimist]: https://badgen.net/bundlephobia/dependency-count/minimist?label=
[depcount-mri]: https://badgen.net/bundlephobia/dependency-count/mri?label=
[depcount-yargs-parser]: https://badgen.net/bundlephobia/dependency-count/yargs-parser?label=

---

[^note-unsupported]: Some libraries may provide workarounds for a feature. This table only attempts to describe features that are supported out of the box.
[^note-yargs-parser]: I only looked at the browser-specific bundle of yargs-parser. The CommonJS bundle uses several Node.js APIs and cannot be bundled for Rhino.

## Explanation of Features

### Dependency count

Dependencies increase the size of the bundle beyond the library itself. In addition, dependencies may pull in Node.js modules or use async code, which is explained below.

### Node.js APIs used

Node.js APIs do not exist in environments such as Rhino or web browsers. When bundling, they must be polyfilled (which adds significant overhead and complexity) or removed (adds complexity). Some features may be impossible to polyfill at all.

### Async code

Rhino does not natively support `setTimeout()`, promises, or `async`/`await`, and polyfilling is impossible without `setTimeout()`. Any libraries that use such code are unusable in Rhino.

### Accept single string

All libraries accept an array of strings, i.e. `process.argv.slice(2)`. However, some libraries can accept a single string (e.g. `"--foo --bar"`) and split it themselves.

Note that this feature can be easily provided by another library, such as [string-argv](https://github.com/mccormicka/string-argv).

### Negative options

Many libraries can parse `--no-some-option` as a _negative_ boolean alias of `--some-option`.

### Camelcase alias

Some libraries can alias `--long-option-name` to `longOptionName` in the returned object. It's not a required feature, but a nicety.

### Count repeated options

Some libraries can count the number of occurrences of a boolean option is given. For example, `-vvv` is parsed to `{ v: 3 }`.

### Stop at first non-option

Many parsers can stop parsing once they encounter a non-option token. For example, when given `--foo --bar stuff --baz`, they will treat `stuff` and `--baz` as non-option tokens. This can be useful when implementing git-style subcommands.

### Discriminate tokens after `--`

Some libraries can discriminate non-option tokens before and after a `--` token. For example:

```
foo --bar -- --baz qux
```

may be parsed to:

```js
{
  bar: true,
  _: ['foo'],            // Before '--'
  '--': ['--baz', 'qux'] // After '--'
}
```

...instead of:

```js
{
  bar: true,
  _: ['foo', '--baz', 'qux'] // Every non-option is here
}
```

This is useful when building a program that passes arguments to another program.

### Dotted option to object

Some argument parsers will parse dotted options (e.g. `--foo.bar.baz 3`) into nested objects:

```js
{
  foo: {
    bar: {
      baz: 3;
    }
  }
}
```

While some may find this feature useful, others may deem it unnecessary as it introduces unnecessary complexity to the library. Also, these features can become a source of prototype pollution vulnerabilities. See:

- <https://snyk.io/vuln/SNYK-JS-MINIMIST-559764>
- <https://snyk.io/vuln/SNYK-JS-YARGSPARSER-560381>

### Custom conversion functions

Some libraries allow converting option values using callbacks.
