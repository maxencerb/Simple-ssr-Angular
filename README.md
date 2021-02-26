---
title: Angular Server Side Rendering (with hosting)
author: Maxence Raballand
date: February 26, 2021
---

# Angular Server Side Rendering (with hosting)

Written by &copy; Maxence RABALLAND, 2021, [My website](https://maxenceraballand.com).

Based on [Angular documentation](https://angular.io/) and [Angular universal docs](https://angular.io/guide/universal).
More information about hosting and pricing on [firebase](https://firebase.google.com/).

<style>
  .images {
    display: flex;
    justify-content: space-around;
  }

  .images img {
    width: 5rem;
  }
</style>

<div class="images">
  <img src="https://nodejs.org/static/images/logo.svg"/>
  <img src="https://angular.io/assets/images/logos/angular/angular.svg"/>
  <img src="https://www.gstatic.com/devrel-devsite/prod/vbd904f2719533e871e3800dda1bebc56aa0bc95c3c9d01c4d7cebcf129bdf26c/firebase/images/touchicon-180.png"/>
</div>

## Table of contents

- [Prerequisites](#prerequisites)
- [Try this project on your environment](#try-this-project-on-your-environment)
- [Create the project](#create-the-project)
  - [Simple angular project](#simple-angular-project)
  - [Add angular universal](#add-angular-universal)
  - [Add a firebase server](#add-a-firebase-server)
  - [Deploy your app](#deploy-your-app)
- [Development Tips](#development-tips)
  - [Faster development method](#faster-development-method)
  - [Creating a component, service, etc](#creating-a-component-service-etc)
  - [Create a 404 page](#create-a-404-page)
  - [Custom express api endpoints](#custom-express-api-endpoints)

## Prerequisites

You'll have to create a **firebase** account first.
Then install npm (find on **node.js** website) and **Angular** on your local machine :

```bash
npm i -g @angular/cli
```

Then, **create your firebase project**. You have to enable **firebase hosting**, and **firebase functions**. When you create your buckets, make sure to choose the right region *(e.g. : eu-west3, us-west1, ...)*.

To enable firebase functions, you'll have to switch to firebase **blaze plan**. It's the pay as you go plan. For more info on pricing, visit [firebase pricing page](https://firebase.google.com/pricing).

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

You'll be prompted with the following :

![alt text](https://raw.githubusercontent.com/maxencerb/Simple-ssr-Angular/master/mdassets/nguniversal.png "nguniversal added to project command image")

So, this will create the following folder structure :

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

![alt text](https://raw.githubusercontent.com/maxencerb/Simple-ssr-Angular/master/mdassets/ngfire1.png "ngfire prompt for angular universal image")

Then simply choose the firebase project you created for your app :

![alt text](https://raw.githubusercontent.com/maxencerb/Simple-ssr-Angular/master/mdassets/ngfire2.png "ngfire prompt to choose firebase project image")

Once done you'll be **all set**.

### Deploy your app

Last step is to deploy your app. Just run the command :

```bash
ng deploy
```

This will build your project for production and deploy to firebase functions.

## Development Tips

### Faster development method

When using the server-side rendering local server, each time you modify your files, it will rebuild parts of the server. It can take a long time before it refreches in your browser.

If you are modifying your angular app only, I recommend using the `ng serve` command. The hot reload is much faster.

Then, if you want to develop server features, or debug your whole app, use `npm run dev:ssr`. It takes longer to refresh, so use it only if necessary.

### Creating a component, service, etc

As you can see, in your `src/app` folder, there is now 2 app module : `app.module.ts` for your app and `app.server.module.ts`. When creating a component or a service, angular will want to auto-import to an app module. So in the cli, you'll have to specify which one to choose. You might want to use `app.module.ts` in most case.

To create a component enter :

```bash
ng g c new-component --module app

or

ng g c new-component --module app.server
```

The same applies for module and services.

### Create a 404 page

When you make a request to a non-existing route while using server-side rendering, the server is trying to create an html template with a non-existing angular route and create a useless workload for your server.

Create a **NotFoundComponent** :

```bash
ng g c components/not-found --module app
```

Make sure your `router-outlet` markup is top-level on your `app.component.html`

```html
<!-- app.component.html -->
<router-outlet></router-outlet>

<!-- not-found.component.html -->
<h1>404 - not found</h1>
```

Feel free to add your own content and styling.
Now add this to your `app-routing.module.ts` :

```ts
import { NgModule } from '@angular/core';
import { RouterModule, Routes } from '@angular/router';
import { NotFoundComponent } from './components/not-found/not-found.component';

const routes: Routes = [
  // your routes here
  {path: '404', component:NotFoundComponent},
  {path: '**', redirectTo:'/404'}
];

@NgModule({
  imports: [RouterModule.forRoot(routes, {
    initialNavigation: 'enabled'
})],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

It is important to add your **custom routes before** these because the request will search for the first matching pattern.

### Custom express api endpoints

You can make modifications to your server settings in the `server.ts` file. To add custom api endpoints, add them before the built-in endpoints. This will overwrite them.

```ts
// server.ts
...
export function app(): express.Express {
  const server = express();
  const distFolder = join(process.cwd(), 'dist/<your-project>/browser');
  const indexHtml = existsSync(join(distFolder, 'index.original.html')) ? 'index.original.html' : 'index';

  server.engine('html', ngExpressEngine({
    bootstrap: AppServerModule,
  }));

  server.set('view engine', 'html');
  server.set('views', distFolder);

  // Add custom endpoints here
  server.get('/api/hello', (req, res) => { 
    // response example
    res.send(`hello from maxence, the current time is ${Date.now()}`);
  });
  // End custom endpoints

  // built-in endpoints
  server.get('*.*', express.static(distFolder, {
    maxAge: '1y'
  }));

  // All regular routes use the Universal engine
  server.get('*', (req, res) => {
    res.render(indexHtml, { req, providers: [{ provide: APP_BASE_HREF, useValue: req.baseUrl }] });
  });

  return server;
}
...
```
