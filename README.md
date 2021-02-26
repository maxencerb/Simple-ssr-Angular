---
title: Angular Server Side Rendering (with hosting)
author: Maxence Raballand
date: February 2021
---

# Angular Server Side Rendering (with hosting)

Written by &copy; Maxence RABALLAND, 2021, [My website](https://maxenceraballand.com).

Based on [Angular documentation](https://angular.io/) and [Angular universal docs](https://angular.io/guide/universal).
More information about hosting and pricing on [firebase](https://firebase.google.com/).

## Prerequisites

You'll have to create a **firebase** account first.
Then install npm (find on **node.js** website) and **Angular** on your local machine :

```bash
npm i -g @angular/cli
```

## Try this project on your environment

Run the following commands on your command line tool :

```bash
git clone https://github.com/maxencerb/Simple-ssr-Angular.git
cd Simple-ssr-Angular
npm i
```

You can open your code editor `code .` and run the angular app **locally** with `ng serve --open` or run the **dev server** with `npm run dev:ssr`

## Create the project

### Simple angular project

To start just use the **angular cli tools** to create a simple project. I'm am using latest versions of angular and angular universal to date (Feb. 2021).

```bash
ng new my-app
cd my-app
code .
```

This will create a simple angular project and open your favorite code editor. During development, you'll be able to use the `ng serve --open` command to serve your app locally and test with hot reload.

### Add angular universal

With the latest version of angular, there is now a built-in angular command that adds the packages and remodel your folder structure.

```bash
ng add @nguniversal/express-engine
```

This will create the following folder structure :

```diff
src/
  ...
+ main.server.ts
  ...
  app/
    ...
+   app.server.module.ts
...
+server.ts
```

Now you can **run your server locally** with `npm run dev:ssr`.

### Add a firebase server

First, **create your firebase account** and **create your project**.
Then **install the firebase cli** with npm and log in to your firebase account :

```bash
npm i -g firebase-tools
firebase login
```

This will prompt you with a browser to **sign in to your google account**.

For **CI (continuous integration)**, refer to the firebase docs and login with `firebase login CI`.

Now **add firebase to yout project** :

```bash
ng add @angular/fire
```

This command will detect that you are on angular universal project. Wen asked if true, say Yes.
