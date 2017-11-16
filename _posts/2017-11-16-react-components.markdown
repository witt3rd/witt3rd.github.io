---
layout: post
title:  "Reusable React Components"
date:   2017-11-16 09:07:14 -0800
categories: React NPM
---
[Facebook's React.js UI framework](https://reactjs.org/) takes a component-based approach to building user experiences.  We'd like to easily share and reuse these components.  Based on [Noel Broda's article][https://medium.com/@BrodaNoel/how-to-create-a-react-component-and-publish-it-in-npm-668ad7d363ce], I have published two simple repositories:

- [React Component Boilerplate][https://github.com/witt3rd/react-component-boilerplate]
- [React Component Boilerplate Test][https://github.com/witt3rd/react-component-boilerplate]

After cloning/forking these repos, it's a good idea to edit the `package.json` files to change the names and repositories.  Of course, you'll need to install the dependencies in the usual way: `npm i`.

## Component Boilerplate

The boilerplate has everything needed for a simple component or component library.  In `./src/index.js` contains a single exported React Component, `Fancy`, that simply emits a `div`.  It is possible to export additional components.

Since we are publishing this component, we must first `npm run build` it, which produces its output in the `./build` directory.  This component can now be pushed back to Github or published to an NPM registry.

We assume here the component has been published to Github.  To install it in another project, simply: `npm i github:witt3rd/react-component-boilerplate.git` (or whatever you've named your component).

## Smoke Testing The Component

To see if your component can be installed and used in a React project, switch to the test project which provide a very simple [Next.js][https://github.com/zeit/next.js] application that depends on the above component (ensure you've changed the names to match).

See `./pages/index.js` for an example use of the component, which simply imports the component and uses it as you would expect.

To see it in action: `npm run dev`.

Happy reuse!
