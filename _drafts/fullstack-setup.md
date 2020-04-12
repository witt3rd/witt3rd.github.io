---
layout: post
title: "Fullstack Setup: React, Express, GraphQL, MongoDB, TypeScript"
slug: fullstack_setup
---

## Introduction

I have spent hours following blog posts and YouTube videos on getting an end-to-end development environment going that has all the features I want:

- TypeScript with hot reload, including when GraphQL schema changes
- React with GraphQL support using hooks - not MobX, Redux, ...
- MongoDB and Mongoose using TypeScript

So, I decided to bite the bullet and build up a full stack template.

## Prerequisites

Assuming your environment is already setup and up-to-date:

- Visual Studio Code
- NodeJS/NPM: NVM and NPX

I'm on a Windows machine, but I prefer the WSL and Bash shell. From within the VS Code terminal, let's create a new project root directory, where the Express/GraphQL server is, and a sub-directory for the React frontend:

```bash
mkdir fullstack
cd fullstack
mkdir frontend
```

In development, we'll be running two independent servers - backend and frontend - so that we get the benefits of hot reloading.

For production, we'll build a static version of the React app and serve it directly from Express.

## Backend

Let's start with the Express server. We'll add GraphQL and MongoDB later.

```bash
# Create a new npm project and accept all defaults
npm init -y

# Install some basic dependencies
npm i express dotenv cors helmet
```

- [express](https://expressjs.com/) is the minimalist web framework for Node.js
- [dotenv](https://github.com/motdotla/dotenv) loads environment variables from a `.env` file
- [cors](https://github.com/expressjs/cors) is an Express middleware module that enables Cross-Origin Resource Sharing (i.e., requests from other servers)
- [helmet](https://github.com/helmetjs/helmet) is another middleware module that provides protection against certain types of attacks by setting various HTTP headers

We need to install some development dependencies to support TypeScript, hot-reloading, and to provide type definitions to the modules we just installed:

```bash
npm i -D typescript concurrently cpx nodemon
npm i -D @types/node @types/express @types/dotenv @types/cors @types/helmet
```

- `concurrently` allows a single npm command to execute multiple commands
- `cpx` will watch for files and copy them if anything changes
- `nodemon` that watches for changes and restarts node

> And, instead of hard-coding values, we'll use environment variables loaded from an `.env` file:

```bash
echo "PORT=7000" > .env
```

> Since the `.env` file is also going to contain some secret information, like our API keys, we'll be sure to exclude it from the git repository by adding it a `.gitignore` file:

```bash
echo ".env" > .gitignore
```

> Speaking of git, let's go ahead an initialize the repo:

```bash
git init
```

> Now that we have the project seeded, let's load it into a VS Code and continue working from there.

```bash
code .
```

> Let's first edit the `.gitignore` file and exclude the `node_modules` folder

> Next, let's setup some commands in our `package.json` that will do the hot-reloading:

```json
{
  "dev": "concurrently \"npx tsc -w\" \"npm run watchgraphql\" \"nodemon build/index.js\"",
  "debug": "concurrently \"npx tsc -w\" \"npm run watchgraphql\" \"nodemon --inspect build/index.js\"",
  "watchgraphql": "cpx 'src/graphql/schemas/**/*.graphql' build/graphql/schemas/ -w -v"
}
```

- `dev` watches for any changes to the TypeScript files and rebuilds them, runs the npm script `watchgraphql` that watches for any changes to the GraphQL schema definitions and copies them to the build directory, and starts `nodemon`, which will restart node when any of the files in the build directory change

> We want to have the source for the backend in a `src` folder and the build to go into a `build` folder. We also want to target the latest ECMAScript spec. To do this, we need a TypeScript configuration file:

```bash
npx tsc --init --rootDir src --outDir build --lib dom,esnext --target esnext
```

> Finally, let's write our first few lines of code! Create a 'src/index.js' file and edit it:

```typescript
console.log("hello");
```

> Save and let's test

```bash
npm run dev
```

> And we see tha
> Now let's create the React app scaffolding for TypeScript:

```bash
cd frontend
npx create-react-app dynamic-youtube-playlists --template typescript
```

> And let's run it to make sure it works.

```bash
npm start

```

## Resources

- [Create React App](https://create-react-app.dev/)
- [Apollo Express GraphQL API with TypeScript](https://medium.com/@th.guibert/basic-apollo-express-graphql-api-with-typescript-2ee021dea2c)
- [How to create a React frontend and a Node/Express backend and connect them](https://www.freecodecamp.org/news/create-a-react-frontend-a-node-express-backend-and-connect-them-together-c5798926047c/)
- [Build a GraphQL React App with TypeScript](https://levelup.gitconnected.com/build-a-graphql-react-app-with-typescript-9661f908b26)
- [Strongly-Typed Models with Mongoose and TypeScript](https://medium.com/@tomanagle/strongly-typed-models-with-mongoose-and-typescript-7bc2f7197722)
- [MongoDB on macOS](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/)
