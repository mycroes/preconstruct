---
sidebar_position: 3
---

# Building a Monorepo

> **Note:** this tutorial builds upon knowledge in [the Getting Started tutorial](/tutorials) so if you haven't gone through it, you should do that first.

We're going to setup a monorepo that uses Preconstruct to build its packages. We're also going to use [Yarn Workspaces](https://yarnpkg.com/en/docs/workspaces) for linking. Yarn Workspaces isn't the only monorepo linking tool that you can use with Preconstruct, [Lerna](https://github.com/lerna/lerna), [Bolt](https://github.com/boltpkg/bolt) and any other tool that does this will also work.

We have our `a-random-number` package from the [the Getting Started guide](/tutorials) which is awesome but we want to create a new set of packages with some other numbers, `the-number-one` and `the-number-two`. We're going to use a monorepo to manage it because we want to be able to manage all our packages together.

## Setting up Yarn Workspaces {#setting-up-yarn-workspaces}

Before we can use Preconstruct, we need to setup Yarn Workspaces and create our packages.

To setup Yarn workspaces, we're going to create a new directory and make a `package.json` that looks like this.

```json
{
  "name": "not-random-numbers",
  "version": "1.0.0",
  "license": "MIT",
  "private": true,
  "workspaces": ["packages/*"]
}
```

We're also going to create our first two packages.

So we need to create a `packages/the-number-one` directory and `packages/the-number-two` directory.

Now we can run `yarn init` in both of the package directories and answer yes to all the questions.

We'll also need to create our source files.

`packages/the-number-one/src/index.js`

```jsx
export default 1;
```

`packages/the-number-two/src/index.js`

```jsx
export default 2;
```

## Setting up Preconstruct to build all our packages {#setting-up-preconstruct-to-build-all-our-packages}

Now that we have Yarn workspaces and our packages setup, we can install and setup Preconstruct.

```bash
yarn add @preconstruct/cli -W
```

```bash
yarn preconstruct init
🎁 ? not-random-numbers what packages should preconstruct build? packages/*
🎁 ? preconstruct is going to change the main field in your package.json, are you okay with that? (Press <space> to select, <a> to toggle all, <i> to invert selection)the-number-one, the-number-two
🎁 ? would you like to generate module builds? this will write to the module field in your package.json (Press <space> to select, <a> to toggle all, <i> to invert selection)the-number-one, the-number-two
🎁 success initialised project!
```

Preconstruct has asked us some slightly different questions since it's detected that we're in a monorepo. We're going to explain exactly what all these questions mean in the future but for now, answering yes to everything will work fine (TODO: write things explaining that stuff)

### Setting up `preconstruct dev` {#setting-up-preconstruct-dev}

`preconstruct dev` is a command in Preconstruct that lets you import your packages without having to build them every time you make a change or alias the dist files to source files in another tool like webpack, see the [Using Preconstruct dev in a monorepo guide](/guides/using-preconstruct-dev-in-a-monorepo) for more info. If you are using Windows, you need to enable Developer Mode before using `preconstruct dev` by following the instructions [here](/guides/using-preconstruct-dev-in-a-monorepo#windows-developer-mode).

We're going to add it to a postinstall script so that yarn will run it after packages are installed. Our package.json will now look like this.

```json
{
  "name": "not-random-numbers",
  "version": "1.0.0",
  "license": "MIT",
  "private": true,
  "workspaces": ["packages/*"],
  "dependencies": {
    "@preconstruct/cli": "^1.0.0"
  },
  "preconstruct": {
    "packages": ["packages/*"]
  },
  "scripts": {
    "postinstall": "preconstruct dev"
  }
}
```

### Adding Babel {#adding-babel}

We'll also want to add Babel so that we can use modern features when writing our code but consumers don't have problems using it like the building your first package tutorial.

We're going to install `@babel/core` and `@babel/preset-env` and add it our Babel config.

```bash
yarn add -W @babel/core @babel/preset-env
```

`babel.config.js`

```jsx
module.exports = {
  presets: ["@babel/preset-env"],
};
```

If you'd like to use TypeScript, read the [Building TypeScript packages guide](/guides/building-typescript-packages). You'll want to _add_ `@babel/preset-typescript` to the `presets` array.

> Curious about why we're using a babel.config.js file here instead of a .babelrc file? Read the [Configuring Babel guide](/guides/configuring-babel)

Now we can run `preconstruct build` to build our packages. 🎉
