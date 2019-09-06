# tsconfigs

[![Greenkeeper badge](https://badges.greenkeeper.io/mightyiam/tsconfigs.svg)](https://greenkeeper.io/)
[![Build Status](https://travis-ci.org/mightyiam/tsconfigs.svg?branch=master)](https://travis-ci.org/mightyiam/tsconfigs)
[![Badge: npm version badge for package `tsconfigs`](https://img.shields.io/npm/v/tsconfigs.svg)](https://www.npmjs.com/package/tsconfigs)

## Overview

This is a set of reusable TypeScript configuration files to extend from.

## Project kind overview

The available *project kind*s are a matrix of two criteria:

1. Whether the project is `importable` or an `end-project`.
1. The runtime environment of the project; `browser`, `nodejs` or `agnostic`.

This results in the following kinds of projects:

- `browser-importable-project`
- `nodejs-importable-project`
- `agnostic-importable-project`
- `browser-end-project`
- `nodejs-end-project`
- ~~`agnostic-end-project`~~ because an end-project needs a runtime, doesn't it?

## Example

In your `tsconfig.json`:

```jsonc
{
  "extends": "tsconfigs/nodejs-end-project",
  "compilerOptions": {
    // Override whatever you like
    "outDir": "lib"
  },
  "include": [
    "src/**/*"
  ]
}
```

## Terms

- *Project kind / kind of project*: One of the configurations that this project exports.
- *Default value*: TypeScript's default value of an option.
- *Our value*: tsconfig's value of an option in a particular *kind of project*.

## Experimental features

Experimental features are outside of the scope of this project. So no experimental feature option is turned on.

## Strictness

We presume that strict type checking is generally desirable.

New type checking features in future releases of TypeScript are, per policy, turned off by default, for backward compatibility. Effectively making new type features, *opt-in*.

The `strict` option, however, turns on a set of strict type checking options. New strict options from future TypeScript releases will be included in it, effectively making new type checking features *opt-out*.

tsconfigs maintains the *opt-out* behavior: we turn `strict` on and yet keep the individual type check options that it includes, off.

## Paths

We would love to include some [path options like `include` and `outDir`](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html#details), but, we feel that it would not be reliable, because TypeScript resolves relative paths from the configuration file in which they appear and not from the end-configuration file. See [this issue](https://github.com/mightyiam/tsconfigs/issues/83).

## Options common to all project kinds

### allowSyntheticDefaultImports (default: depends): `true`

```ts
import foo from 'foo'
```

seems nicer than

```ts
import { default as foo } from 'foo'
```

Recommended reading: [this Stack Overflow question](https://stackoverflow.com/questions/56238356/understanding-esmoduleinterop-in-tsconfig-file)

### esModuleInterop (default: `false`): `true`

```ts
import { bar } from 'foo'
```

Seems nicer than

```ts
import * as foo from 'foo'
const { bar } = foo
```

Recommended reading: [this Stack Overflow question](https://stackoverflow.com/questions/56238356/understanding-esmoduleinterop-in-tsconfig-file)

### forceConsistentCasingInFileNames (default: `false`): `true`

While this does not force case sensitivity, it at least enforces consistent casing.

### moduleResolution (default: depends): `"node"`

TypeScript default is `"Classic"` if `module` is `"AMD"` or `"System"` or `"ES6"` otherwise `"Node"`.

The most popular choice.

### newLine (default: depends): `"LF"`

TypeScript default is platform specific.

For the sake of consistent build artifacts cross-platform.

### noErrorTruncation (default: `false`): `true`

Screenshot with `false`:

![An error message with truncated parts](assets/noErrorTruncation.false.png?raw=true)

Screenshot with `true`:

![A full error message](assets/noErrorTruncation.true.png?raw=true)

### resolveJsonModule (default: `false`): `true`

Seems like a popular feature that does not involve drawbacks.

### sourceMap (default: `false`): `true`

Source maps make debugging easier. We have chosen normal source maps, rather than inline source maps or inline sources, because it seems that it's the simple choice that would serve most projects.

### strict (default: `false`): `true`

See [`Strictness`](#strictness).

### target (default: `"es3"`): `"esnext"`

We feel that the JavaScript world is stepping away from using TypeScript for down-transpilation in turn for Babel. Also, setting a specific ES version here would mean more frequent breaking changes in releases of this project. Also, we feel that for any kind of project (link to project kinds) we could not predict a desired target.

## End-project options

### end-project composite (default: `true`): `false`

Because it seems to have no benefit for end-projects and it necessitates generation of declaration files, which seem useless in end-projects.

## Importable project options

### importable declaration (default: `false`): `true`

Because we'd like to provide the importer with type definitions.

## Browser options

### browser lib

- TypeScript default depends on `target`.
- Our default is `["ESNext","DOM","WebWorker"]`.

### browser module

- TypeScript default depends on `target`.
- Our default is `"ESNext"`.

## Node.js options

### nodejs lib

- TypeScript default depends on `target`.
- Our default is `["ESNext"]`.

You'd most likely also like to install the [`@types/node` package](https://www.npmjs.com/package/@types/node).

### nodejs module

- TypeScript default depends on `target`.
- Our default is `"CommonJS"`.

## Environment-agnostic project options

### agnostic lib

- TypeScript default depends on `target`.
- Our default is `["ESNext"]`.

### agnostic module

- TypeScript default depends on `target`
- Our default is `"CommonJS"`

While for small packages, CommonJS could be just fine, for larger packages, where the ability to perform tree shaking is desirable, it seems that the agnostic project author should consider providing two builds. One CommonJS build and one ES modules build.
