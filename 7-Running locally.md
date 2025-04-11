
# Running your app 

Running your application means to manifest your local App code into a working Application on the browser window. 

# 1. Running your Frontend React App

There are 5 steps needed to run your React application :

i. `Building`  
&nbsp;&nbsp; a] Bundling all .js files  
&nbsp;&nbsp; b] Injected bundled js into the index.html file  
ii. `Creating a server`  
iii. `Serving` - i.e. the created server loads the built app onto a /localhost:PORT in the browser  
iv. `Rendering`  
v. `Routing` - i.e. changing the webpage based on requested path

CRA and Vite are two popular tools for building+serving React Apps.

## i. Building the app

Your React App firstly contains JSX/Typescript which unlike pure JS, Node can't run directly. Secondly this JSX is injected into the root of an `index.html` file which Node cannot run. Hence Vite creates an optimized build of the app which the browsers can play.

The bundling+injection process happens when you run `npm run dev` (for Vite), or `npm run start` (for CRA). The `/public/index.html` file is injected with bundled javascript and CSS within its `<body>`.  
CRA uses the build tool Webpack, whereas Vite uses esbuild. esbuild owes its speed to the fact that it is written in Go — a fast, multithreaded language compiled to machine code, unlike Webpack, written using JavaScript, which is interpreted and single-threaded, which in turn makes it slower for larger projects. Moreover with Vite, changes in your code trigger rapid hot-module reloading (HMR), leading to near-instantaneous updates in the browser. This is a significant speed advantage during development.  

When you run `npm run dev`, Vite doesn't create a separate build folder like you would with a traditional build process. Instead, Vite uses a technique called "in-memory bundling" or "on-the-fly bundling".

## ii. Creating a server

When you run `npm run dev` command, Vite first `builds` and then also creates a `development server` on-the-fly.

## iii. Serving the app

 That created server now serves the injected `/public/index.html` file to localhost:PORT, where PORT is usually 5173 by default but can be changed in the `vite.config.js` file. All of this happens on-the-fly which is why you cannot "see" any build or server folders/files made.

## iv. Rendering

Note that the `/frontend/public/index.html` file is just a 'shell' as discussed earlier. What the client sees on the browser screen is the `rendered` version of this index.html where all the nested React components come alive through the `root div element`. So `rendering` is the process of expansion of this 'shell' index.html file.

When the index.html was served in the last step alongwith `/src` components, rendering happens in the browser (client) itself. As we'll see later this is called `Client-Side Rendering`.

## v. Routing

If you want to create an application with multiple page routes, eg.
```
/
├── home/
├── about/
├── contact/
└── services/
```
you should do routing. This is also called `client-side routing`. 

Vite doesn't perform routing by default. So you can import an external React library `react-router-dom` (separate from the `react` library, to handle routing. Refer to [this](https://hygraph.com/blog/routing-in-react) tutorial on Routing for clarity.

So the server `serves a single index.html file`, which acts as a "shell/template" for the application, while the `React Router` (ie.`react-router-dom` library) ensures that routing happens dynamically and the corresponding component can be rendered without making a new request to the development server. 

Moreover, most modern HTML5 browsers are powered with the `History API` which has useful methods like `pushState()` to interact with your hosted React SPA. So when a user clicks a link or performs an action that changes the view, React Router can use `pushState()` to update the URL in the address bar. This gives the appearance of navigating to a new page while keeping the application running in the background.

So the React Router performs routing in two ways:

### 1. Traditional way: `<Route>s` within `<Routes>`

Here the `BrowserRouter` component wraps your application and interacts with History API of your browser.
The built-in `<Routes>` component is a container for all your `<Route>` elements.

```js
//App.js

import React from 'react';
import ReactDOM from 'react-dom/frontend';
import { BrowserRouter, Routes, Route } from 'react-router-dom';
import Home from './Home';
import About from './About';

const App = () => {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
      </Routes>
    </BrowserRouter>
  );
};

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

### 2. Modern way: Using `createBrowserRouter` and `RouterProvider`

This is suitable for larger applications. Here `createBrowserRouter` function accepts an array of route objects and creates a `router` instance, and the built-in `RouterProvider` component provides that `router` to your application.

```js
//App.js

import React from 'react';
import ReactDOM from 'react-dom/frontend';
import { createBrowserRouter, RouterProvider } from 'react-router-dom';
import Home from './Home';
import About from './About';

const router = createBrowserRouter([
  {
    path: '/',
    element: <Home />,
  },
  {
    path: '/about',
    element: <About />,
  },
]);

const App = () => {
  return (
    <RouterProvider router={router} />
  );
};

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(<App />);
```

# 2. Running a Backend Express App

Like Frontend, there are 5 steps needed to run your backend application :

## i. Building the app

`NOT REQUIRED`, as here our server file `index.js` and other Routes & Controllers files are all pure JS, without any JSX/React-like components and there's no .html file. So Node.js can directly run it without the need for a compilation or build step.


## ii. Creating a server

Unlike a Frontend React App where the development server is auto-created by Vite as explained [above](#ii-serving), in the backend you need to manually create your own server as an `index.js` file as explained [here in Chap 6-Backend](./6-Backend.md#1-setting-up-a-web-server-using-expressjs-framework).   
(N.B. if you have a serverless backend like Firebase, then it's not needed.)

## iii. Serving the app

Run `node index.js` and your server will serve your Express app.

N.B. If you need Hot Module Replacement (HMR) just like Vite automatically provides for your frontend app, you need to use `Nodemon`. It is basically an alternative to running `node index.js`. Refer [here](https://pastebin.com/Qqdp8WdD) for a tutorial.

## iv. Rendering

`Not applicable` since there are no html files in the backend.

## v. Routing

Unlike your frontend React App where Routing is needed to shift between different `navigation paths`, in your backend there are no static files/pages/components and so here Routing means to handle HTTP Requests at different API `Endpoints`. For example, an Express app might have endpoints like `/api/users` to fetch user data or `/api/products` to retrieve product information. This backend routing was covered in detail in [Chap 6-Backend here](./6-Backend.md/#2d-routing).

#  3. Running Frontend & Backend apps simultaneously

Eventually in `production mode`, you'll have to run both frontend and backend applications simultaneously, where the entire `application flow` from user input to backend processing back to the user input happens seamlessly.

First, you need to learn how to test this integration (`development setup`) and then how to finally deploy your fullstack app (`production setup`). So let's learn.

## i. DEVELOPMENT SETUP

During development of your fullstack application, you may need to test the application flow, API calls and real-time data updates between frontend and backend, as well as error handling and debugging. So here's how you setup:

### a. Frontend/backend separation

1. Keep frontend files in `/frontend` (Vite project)
2. Keep backend files in `/backend` (Express API)
3. Run them as separate services:
    * Vite dev server (port 5173)
    * Express server (port 3000)

### b. Proxy configuration

Add this to vite.config.js:

```js
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true
      }
    }
  }
})
```

This routes all `/api` requests to Express while serving frontend assets through Vite.


## ii. PRODUCTION SETUP

Here we want to use a single server to serve both our frontend and backend apps. 

Firstly, remember that Vite is only a developmemt server but Express is a production grade web server. So we'll use our Express server to serve both frontend and backend.

Secondly, remember that Nodejs can run pure JS only. So we need a way to take all our frontend code (html/css/jsx, etc.) and convert it to pure JS to be served. The `express.static()` middleware does that job.

Thirdly, `express.static()` middleware is designed for serving static assets—files that don’t change dynamically (eg. html, css, js, images), but not dynamic routes like `/about`, `/contact-me`, etc. of the frontend. So whenever a GET request to any route comes from the client browser, we just need `express.static()` to host the main `index.html` of frontend to `/`. And then index.html has [React Router](#iv-routing) to detect the clicked route and take the client to that page. So our job will be done.

With that mind-map, let's go.

### a. Building the frontend app

Build the frontend app with `npm run build`. This creates a `/frontend/dist/` or `/frontend/build` folder.

```
/dist
│
├── static                # Contains static assets
│   ├── css               # CSS files
│   │   └── main.css      # Main CSS file
│   ├── js                # JavaScript files
│   │   ├── main.js       # Main JavaScript bundle (often referred to as bundle.js)
│   │   └── runtime-main.js # Runtime bundle
│   └── media             # Media files (images, fonts, etc.)
│       └── logo.png      # Example image
│
├── index.html            # Main HTML file
├── favicon.ico           # Favicon for the application
└── manifest.json         # Web app manifest for PWA support
```
where the `/static` folder contains all optimized CSS,JS and supported code mixture.

### b. Serving frontend from the backend

Serving the frontend static build from Express server.

In your Express server file `index.js`:

```js
//This uses the express.static() middleware to serve static files from /dist. The index.html in /dist would be hosted in the root '/' 
app.use(express.static(path.join(__dirname, '../frontend/dist')))

//This handles any GET request to any route (* is a wildcard) by sending the index.html file from the /client/dist directory
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '../client/dist/index.html'))
})
```

Then simply run the server using `node index.js`

### c. Routing API endpoints

But note that the app.get('*', ...) route would catch all GET requests, including API endpoints like /api/users/ or /api/products/. To handle this situation, you need to make sure that API routes are handled by the Express API before the wildcard route catches them.

Here's how you can do it:

Define your API routes before the wildcard route. For example:

```js
app.get('/api/users/', (req, res) => {
  // Handle API request for users
});

app.get('/api/products/', (req, res) => {
  // Handle API request for products
});

// Other API routes...

// Wildcard route to handle client-side routes
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '../client/dist/index.html'))
});
```

By defining the API routes before the wildcard route, Express will check if the incoming request matches any of the API routes first. If it does, the API route handler will be executed. If it doesn't, the wildcard route will catch it and send the index.html file.

Alternatively, you can use a separate router for API routes. For example:

```js
const apiRouter = express.Router();

apiRouter.get('/users/', (req, res) => {
  // Handle API request for users
});

apiRouter.get('/products/', (req, res) => {
  // Handle API request for products
});

// Other API routes...

app.use('/api', apiRouter);

// Wildcard route to handle client-side routes
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '../client/dist/index.html'))
});
```

In this example, the API router is mounted at the `/api` path, so any requests to `/api/*` will be handled by the API router. The wildcard route will only catch requests that don't match any of the API routes.

By using one of these approaches, you can ensure that API requests are handled by the Express API and client-side routes are handled by the React application.

------------

Note that Express API can even handle some client-site routes where it can provide dynamic response based on user input (eg. name provide in the URL), eg.: 
```js
app.get('/greet/:name', (req, res) => {
  const name = req.params.name;
  res.send(`<h1>Hello, ${name}!</h1>`); // Respond with a personalized greeting
});
```
This is called `Dynamic serving` whereas it's called `Static serving` when we [previously](#b-serving-frontend-from-the-backend) served our frontend static build. Because Express can serve these non-API dynamic routes as well, you can infact add an index.html file within your `/backend` folder and create a standalone webapp purely with backend !

### d. Server Side Rendering (SSR)

We had read about Client-Side Rendering (CSR) [earlier](#iv-rendering).

* We've been doing CSR till now :

Till now, in either the Development or Production setup, whenever after your Frontend App is `built`, and the client (browser) would request a page, the server (separate or integrated) would send the contents of the `/dist` or `/build` folder (containing index.html and supporting JS) to the client (browser). Once the browser executes the supporting JS, the html is rendered and the webpage starts behaving like the complete interactive SPA. This sort of rendering of the html file is called `Client-Side Rendering (CSR)`.

* The problem with CSR :

HTML loads much faster than the supporting JS. But in CSR, it has to wait for the browser to execute JS so that HTML can be rendered together. Only then the complete app comes alive at once in the browser.

* What's the solution?

If we could pre-render the minimal static html in the server itself and load and display it in the browser instantly, while letting the JS to thereafter slowly execute and enhance (`hydrate`) this html so that the web-page eventually behaves like a fully interactive SPA, it'll be great. This process where html rendering happens on the server before the content reaches the client is called `SSR`. 

For SSR, our server `/backend/index.js` file would have this:

```js
// /backend/index.js

import express from 'express';
import React from 'react';
import { renderToString } from 'react-dom/server';
import App from '../frontend/src/App'; // Adjust the import path

const app = express();

// Serve static files from the client build
app.use(express.static('../client/public'));

// Handle requests
app.get('/', (req, res) => {
    const appString = renderToString(<App />);  // useful function to change <App /> component into a String
    const html = `        //this html variable would be sent as response to client
        <!DOCTYPE html>
        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <meta name="viewport" content="width=device-width, initial-scale=1.0">
            <title>SSR with Express and React</title>
        </head>
        <body>
            <div id="root">${appString}</div>   //<App/> component inserted as a string in the root div element
            <script src="/bundle.js"></script>  //ref dir tree of /build folder
        </body>
        </html>
    `;
    res.send(html);
});

// Start the server
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server is running on http://localhost:${PORT}`);
});
```

As you can see in the server js file above , we have smartly converted the imported `<App />` component to a static HTML string and inserted an html code, injected with that App String in the root div element (in the React style) using an `html variable`. Thus we've created rendered html within our server itself.

Also note that though the `bundle.js` we got after Frontend build is sourced here, but it will hydrate the rendered html only after being sent to the client/browser later on.

However our Nodejs won't be able to run this server js file as it's not pure JS anymore (it has html, React jsx stuff). So you'll need to bundle it using Webpack & Babel as told in Step 3 of [this tutorial](https://www.digitalocean.com/community/tutorials/react-server-side-rendering).

TL;DR: in SSR, html `rendering` doesn't wait for JS `hydration`. By instantly loading the minimal html, search engines can more easily find this page improving SEO compared with CSR.

### e. Static Site Generation (SSG)

Even in SSR, initial html is rendered from the server but JS hydration takes time. What if we could render our entire app (html+JS) beforehand.......So we've read about CSR and SSR. But there is a third way to render your app: SSG. This is based on JAMStack.

JAM stands for JavaScript, APIs, and Markup. It’s not a tech stack like MERN or LAMP—rather, it’s a modern architecture for building fast, secure, and scalable websites. 

Key features of a JAMStack application:

- To pre-render your code as much as possible so your site can be served from CDNs for ultra-fast load times.
- Replace your Express backend completely with 3rd party API calls *(ie. fetching from public APIs or a headless CMS)* and serverless functions.
- Have pre-rendered HTML (aka "markup") instead of rendering client-side (CSR) or server-side (SSR) at runtime.