---
category: programming
title: "Migrating from create-react-app to Vite"
tags:
  - javascript
  - cli
  - vite
---

I recently converted a TypeScript+React project generated with [create-react-app] to [Vite](http://vitejs.dev/). Here's what I did.

<details markdown="1">
<summary>Table of Contents</summary>
* TOC
{:toc}
</details>

## Why?

My laptop is slow. It takes 3 minutes to cold-start Webpack's development server, and ~1 minute to restart it.

## What I did

1. I generated a fresh starter template using `yarn create @vitejs/create-app`, then chose "react" and "typescript" for my framework and language choices.
2. I copied `vite.config.ts` from the generated project to my own.
3. I copy-pasted lines from the generated `package.json`, `tsconfig.json`, and `index.html` to those in my project. I also moved `index.html` from `public/` to the project root.
4. I ran `yarn install` inside my project dir to install Vite.
5. I ran `yarn run dev` to start the development server. It launched in \< 0.5 seconds! Crazy!

## Code changes

Unfortunately, the project failed to build in dev mode and needed small code changes.

### Esbuild chokes on generic arrow functions

I immediately got a build error like this:

```
[plugin:vite:esbuild] Transform failed with 1 error:
<project path>/packages/manager/src/components/PanelConfig.tsx:122:6: error: Expected "}" but found ";"
<project path>/packages/manager/src/components/PanelConfig.tsx:122:6
Expected "}" but found ";"
120|
121|  export const PanelConfig = () => {
122|    _s();
   |        ^
123|
124|    const {
    at failureErrorWithLog (<project path>\node_modules\esbuild\lib\main.js:1443:15)
    at <project path>\node_modules\esbuild\lib\main.js:1254:29
    at <project path>\node_modules\esbuild\lib\main.js:606:9
    at handleIncomingPacket (<project path>\node_modules\esbuild\lib\main.js:703:9)
    at Socket.readFromStdout (<project path>\node_modules\esbuild\lib\main.js:573:7)
    at Socket.emit (events.js:314:20)
    at addChunk (_stream_readable.js:297:12)
    at readableAddChunk (_stream_readable.js:272:9)
    at Socket.Readable.push (_stream_readable.js:213:10)
    at Pipe.onStreamRead (internal/stream_base_commons.js:188:23
```

The code in question looks like this:

```ts
export const PanelConfig = () => {
  const {
    data: baseConfig,
    error: loadingError,
    mutate,
  } = useSWR(CONFIG_ROUTE, async () => (await fetchGetConfig()).result);

  //...
};
```

Huh. I see nothing wrong here?

After some tinkering, I found that the code _immediately_ above this function was to blame:

<!-- prettier-ignore-start -->
```ts
const isOneOf = <T,>(value: unknown, compareWith: readonly T[]): value is T =>
  compareWith.includes(value as T);

export const PanelConfig = () => {
  // ...
};
```
<!-- prettier-ignore-end -->

Here, we have a arrow function with a generic parameter `T` in a `.tsx` file. Generic arrow functions are tricky because the syntax is ambiguous with JSX. I was using [a trick](https://stackoverflow.com/a/56989122/) to force TypeScript to treat `T` as a generic type parameter instead of JSX. Unfortunately, [Esbuild] (which powers Vite) doesn't understand this trick.

I fixed it by using a [slightly older version of the trick](https://stackoverflow.com/a/45576880):

```ts
const isOneOf = <T extends unknown>(
  value: unknown,
  compareWith: readonly T[]
): value is T => compareWith.includes(value as T);
```

## Build configuration

Vite requires manually setting up some things that create-react-app and Webpack provided out of the box.

### `process` is not defined

During the transition, I ran into another error. This one appeared in the browser console:

```
Uncaught ReferenceError: process is not defined
    at classes.ts:22
```

When I clicked on the file name, the Sources view lead me to this:

```
Could not load content for http://localhost:3000/node_modules/@blueprintjs/core/src/common/classes.ts (HTTP error: status code 404, net::ERR_HTTP_RESPONSE_CODE_FAILURE)
```

So I assume some code inside Blueprint.js is using [`process`][process], a global object available in Node.js. But why would a browser module use `process`?

Let's look at the [source code](https://github.com/palantir/blueprint/blob/%40blueprintjs/core%403.44.0/packages/core/src/common/classes.ts#L22) of `@blueprintjs/core`:

<!-- prettier-ignore-start -->
```ts
const NS = process.env.BLUEPRINT_NAMESPACE || process.env.REACT_APP_BLUEPRINT_NAMESPACE || "bp3";
```
<!-- prettier-ignore-end -->

...Oh. Right. `process.env` is injected by Webpack.

CRA provides this feature out of the box through its [Webpack configuration](https://github.com/facebook/create-react-app/blob/v4.0.3/packages/react-scripts/config/webpack.config.js#L647), which injects several environment variables using [`webpack.DefinePlugin`](https://webpack.js.org/plugins/define-plugin/). Looks like Vite does not, and we'll have to do it manually:

```ts
// vite.config.ts

export default defineConfig({
  define: Object.fromEntries(
    Object.entries(process.env).map(([name, value]) => [
      `process.env.${name}`,
      // String values must be escaped, since they are used as raw values
      JSON.stringify(value),
    ])
  ),
});
```

With these fixes, the project was built!

### Proxy Configuration

CRA reads the [`proxy` field in `package.json`](https://create-react-app.dev/docs/proxying-api-requests-in-development/) to provide a proxy for the backend during development. On the other hand, Vite [refers to its configuration file for proxy setup](https://vitejs.dev/config/#server-proxy):

```ts
// vite.config.ts
export default defineConfig({
  // ...
  server: {
    proxy: {
      "^/(relay_|images)": "http://localhost:60080",
    },
  },
});
```

Note that CRA does [some heuristics](https://github.com/facebook/create-react-app/blob/v4.0.3/packages/react-dev-utils/WebpackDevServerUtils.js#L423-L430) to check whether a request should be proxied:

1. Is the request _not_ a GET? If so, always proxy.
2. If the request is a GET, proxy if all of the following is true:
   - Is the request _not_ accessing a resource under the `public/` dir, or a WebpackDevSever socket endpoint (for Hot Module Reloading, I assume)?
   - The `Accept` request header does not have `text/html`.

Vite does not perform such heuristics. It simply [compares the URL against each key of the `server.proxy` object](https://github.com/vitejs/vite/blob/v2.3.2/packages/vite/src/node/server/middlewares/proxy.ts#L85-L86). Because of this, we must ensure that our string (or regex) is correct.

With all these issues fixed, `yarn run dev` (which runs `vite`) now ran smoothly. Time to build...

### Duplicate identifier `src`

I ran into errors when I executed `yarn run build` (which runs `tsc && vite build`). TypeScript complained:

```
../../node_modules/react-scripts/lib/react-app.d.ts:24:18 - error TS2300: Duplicate identifier 'src'.

24   export default src;
                    ~~~

  ../../node_modules/vite/client.d.ts:115:18
    115   export default src
                         ~~~
    'src' was also declared here.
  ../../node_modules/vite/client.d.ts:119:18
    119   export default src
                         ~~~
    and here.
  ../../node_modules/vite/client.d.ts:123:18
    123   export default src
                         ~~~
    and here.
  ../../node_modules/vite/client.d.ts:127:18
    127   export default src
                         ~~~
    and here.
  ../../node_modules/vite/client.d.ts:131:18
    131   export default src
                         ~~~
    and here.
```

These errors originate from the type definitions provided by `vite`[^vite-typedef] and `react-scripts`[^react-scripts-typedef], which enable assets to be imported in TypeScript code. The `tsconfig.json` generated by Vite [explicitly disables `compilerOptions.skipLibCheck`](https://github.com/vitejs/vite/blob/v2.3.2/packages/create-app/template-react-ts/tsconfig.json#L7), which causes these conflicts to surface.

Why was my project pulling in types from `react-scripts`? The culprit was a `react-app-env.d.ts` that I had forgotten.

The solution is simple: Delete `react-app-env.d.ts`, which was pulling in types from `react-scripts`. I also removed `react-scripts`, which I had been keeping around "just in case".

```console
$ rm src/react-app-env.d.ts
$ yarn remove react-scripts
```

The build now succeeds.

[^vite-typedef]: <https://github.com/vitejs/vite/blob/v2.3.2/packages/vite/client.d.ts>
[^react-scripts-typedef]: <https://github.com/facebook/create-react-app/blob/v4.0.3/packages/react-scripts/lib/react-app.d.ts>

### Configuring the output directory

[create-react-app did not support changing the output directory](https://stackoverflow.com/a/41495859/) for the longest time to the [chagrin of many developers](https://github.com/facebook/create-react-app/issues/1354#issuecomment-299956790). Then it begrudgingly learned the `BUILD_PATH` environment variable in [v4.0.2](https://github.com/facebook/create-react-app/releases/tag/v4.0.2). This is slightly cumbersome, however, as it meant that you had to keep a separate `.env` file instead of importing a constant from JavaScript code.

Vite supports it using the [`build.outDir`](https://vitejs.dev/config/#build-outdir) build option. Since Vite's config file is JavaScript code, we can easily import values from other files. Don't forget to set [`build.emptyOutDir`](https://vitejs.dev/config/#build-emptyoutdir) to `true` to replicate CRA's behavior.

### Configuring the base path

create-react-app uses the [`homepage` field in `package.json`](https://create-react-app.dev/docs/deployment/#building-for-relative-paths) to configure relative base paths. I was using `"."` to resolve assets using relative paths.

Vite uses the [`base` build option](https://vitejs.dev/config/#base), which behaves similarly, except that I had to use `"./"` instead of `"."`.

Now the distribution succeeds!

### Generating source maps

create-react-app generates source maps by default. Vite does not, but this can be easily enabled by setting the [`build.sourcemap`](https://vitejs.dev/config/#build-sourcemap) option to `true`.

Unfortunately, [it seems that CSS source maps are unsupported](https://github.com/vitejs/vite/issues/649). I tried setting both `build.cleanCssOptions.sourceMap` and `css.postcss.map` to `true`, but to no avail.

## Miscellaneous changes

CRA adds and uses the [`browserslist` field in `package.json`](https://create-react-app.dev/docs/supported-browsers-features/#configuring-supported-browsers). Since Vite uses the [`build.target`](https://vitejs.dev/config/#build-target) option, we can delete the `browserslist` field.

For ESLint, CRA extends the [`react-app` and `react-app/jest` ESLint configs](https://github.com/facebook/create-react-app/tree/master/packages/eslint-config-react-app). Vite is less opinionated with linting, so you have to build your own linter settings from scratch.

[create-react-app]: https://create-react-app.dev/
[esbuild]: https://esbuild.github.io/
[process]: https://nodejs.org/api/process.html#process_process
