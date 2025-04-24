
# Running your app locally

Running your application means to manifest your local App code into a working Application on the browser window. 

# I. RUNNING YOUR FRONTEND REACT APP

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

Your React App firstly contains JSX/Typescript which unlike pure JS, Node can't run directly. Secondly this JSX is injected into the root of an `index.html` file which Node cannot run. Hence Vite (the build tool we're using) needs to convert all sourcecode into **pure static files** (vanilla html (with/out js hydration) +css+ images,etc) creates an optimized build of the app which the browsers can play.

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

# II. RUNNING A BACKEND EXPRESS APP

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


# III. RENDERING STRATEGIES

Based on when and where rendering is done, we have different strategies:

## 1. SSR (Traditional LAMP Stack)

  * When : At runtime, when the page is requested.
  * Where : On the server (e.g., PHP/Apache)
  * Details : Each request triggers the server to query a database, run PHP, and generate full HTML. Common in older CMSes like WordPress/Drupal/Joomla or custom PHP apps.
  
## 2. CSR (Client-Side Rendering)

  * When: At runtime, when the page loads or user interacts.
  * Where: In the client’s browser.
  * Details : Slow initial load and SEO issues

  You must understand the CSR can only happen because there is clear separation of client and server. This is how it works locally.
  
  ### a. Frontend/backend separation

1. Keep frontend files in `/frontend` (Vite project)
2. Keep backend files in `/backend` (Express API)
3. Run them as separate services:
    * Vite dev server (port 5173)
    * Express server (port 3000)

While deploying, you can host/deploy frontend and backend in separate cloud servers (eg. I use Firebase for frontend & Render for backend)

### b. Proxy configuration

Some people do this step. Although your fullstack app would work even without this. 

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


## 3. SSR (MERN SPA, e.g., React/Next.js with SSR)

  * When : bare-bones html at build-time, JS hydration at run-time
  * Where: bare-bones html in Express Server, JS hydration in Client's Browser
  * Details : Balances SEO with SPA-like navigation. But server is still needed. Works best when frontend is coded in `NextJS`, but simple Reactjs would also do.

  It has two sub-strategies: First is to simply create redirection logic within the server where server redirects frontend route requests to frontend. Second, is to serve a bare-bones html of frontend home page in Express Server itself, JS hydration in Client's Browser, and for other frontend routes simply redirect to frontend.

  ### `Sub-Strategy 1:`

  Here we want to use a single server to serve both our frontend and backend apps. 

Firstly, remember that Vite is only a developmemt server but Express is a production grade web server. So we'll use our Express server to serve both frontend and backend.

Secondly, remember that Nodejs can run pure JS only. So we need a way to take all our frontend code (html/css/jsx, etc.) and convert it to pure JS to be served. The `express.static()` middleware does that job.

Thirdly, `express.static()` middleware is designed for serving static assets—files that don’t change dynamically (eg. html, css, js, images), but not dynamic routes like `/about`, `/contact-me`, etc. of the frontend. So whenever a GET request to any route comes from the client browser, we just need `express.static()` to host the main `index.html` of frontend to `/`. And then index.html has [React Router](#iv-routing) to detect the clicked route and take the client to that page. So our job will be done.

With that mind-map, let's go.

#### a. Building the frontend app

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

  #### b. Serving frontend from the backend

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

#### c. Routing API endpoints

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

### `Sub-Strategy 2: Server Side Rendering (SSR)` 

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


## 4. Static Site Generated (SSG) - 

**------- The core idea of SSG is to pre-render the project (ie. convert to pure static files) at `build-time` instead of `run time` we were doing in CSR and SSR. This would ensure very fast page loads.-------**

We know that `build + API injection = rendering`. So if there are no APIs in the sourcecode, rendering will happen at build time ! 

Again if you insist on using APIs as they give God-level interactivity, better use 3rd party APIs. Because retrieving the REST APIs from your own backend web server would be slow and code-heavy. 

Further if you have your own web server, you'll have to host it separate from the frontend, because it's very difficult to convert its REST APIs which are `dynamic` content, into `pure static files` (vanilla html+ css+ md+ images+ setup files) for rendering alongwith the frontend. 

All of these constraints basically means that solely a site with only own frontend code and no own backend server, can be pre-rendered at build time. Infact `serverlessness` is a key feature of SSG sites. 

The frontend code can be anything - pure static files (vanilla html+ css+ md+ images+ setup files), or static+ vanilla JS, or static+ JS frameworks(eg Reactjs, D3js, etc.).  
Whatever it is, depending on what it is, you need to use some `build tool` aka `static site generator` to render it into **PUREST VANILLA STATIC FILES**. Examples of such build tools are :

* like `Jekyll`: (written in Ruby syntax) for pure static files (esp. md) + liquid templates  
* like `Hugo`: (written in Go syntax) for static files
* like `Eleventy`: (written in JS syntax) for static files
* like `Gatsby` : (written in Reactjs syntax) for static files
* like `Vite`: (written in NodeJS syntax) for static+ Reactjs
* like `Webpack`: (written in JS syntax) for static+ Reactjs

`How to use the build tool:`

In `development mode`, you just serve - it will create a temp build under the hood & serve it on the localhost port) eg. `jekyll serve`, `vite serve`, etc.

In `production mode`, use `build` command eg. jekyll build or vite build, then a `/dist` or `/build` folder is created. You can now upload/host/deploy your entire local project to a PaaS (eg. Firebase, Github pages, Vercel, etc.) usually using the `deploy` command. And boom, your app/site is live.

- **A TRICK TO ABSTRACT THE `BUILD` COMMAND :**

  Often, you might not be sure of the build tool you wish to use. Say you use jekyll, but might shift to hugo tomorrow. So in your `package.json`, add a script like `"dev": "jekyll serve"` and `"build": "jekyll build"` for Jekyll. For other tools, you can replace jekyll build with the respective build command (e.g., hugo, gatsby build, etc.). 

  Then instead of `jekyll serve`, you can run `npm run dev`, and instead of `jekyll build` you can run `npm run build`. If you want to shift to `hugo` tomorrow, just change the package.json script accordingly and the same npm commands keep working as before.

  NB. Instead of `npm run build` and `package.json`, you can use `make build` and `Makefile` esp. for non-JS heavy sites, but that often has dependency issues. 

  NB. GitHub Pages has native support for Jekyll, meaning it automatically builds and deploys Jekyll sites even without a package.json. But that's not true for other PaaSs like Heroku or Firebase which can auto-build but you need to specify the build tool in package.json. It's anyway a good practice to specify because you'll need to `serve` if not `build` in the testing/development phase anyway.


There are two sub-types of SSG sites:   
SSG site without any backend, and,   
SSG site with a serverless backend aka JAMStack.

### Type A. SSG site without any backend

  * **When** : entire static html pre-rendered at build-time by build/CI tools like Jekyll/Gatsby/Hugo/Eleventy.
  * **Where** : PaaSs (Platforms as a Service) which I like to call `CDN++` (i.e. CDN + build(CI/CD) pipeline + serverless functions) - egs. --see list below--
  * **Details** : Delegate all server functionalities to 3rd party APIs and use Fetch APIs from 3rd party apps (eg live weather) can be used. Core backend functionalities like database access or payments, etc. are not supported.

  [*nb. KEY TERMS ARE :    
  CDN => Global network of edge servers for fast delivery of static assets (HTML, CSS, JS, images).  
  CI/CD Pipeline => Automated build and deployment triggered by code changes (e.g., Git commits).  
  Serverless Functions => Backend logic for dynamic features without managing servers.*]

  ### Type B. SSG site with a serverless backend `aka` JAMStack

  * **When** : entire static html pre-rendered at build-time by build/CI tools like Jekyll/Gatsby/Hugo/Eleventy.
  * **Where** : `CDN++` (i.e. CDN + build(CI/CD) pipeline + serverless functions)
  * **Details** : Prefers cloud database/payments/etc. accessed via proprietary `serverless backend functions`/ `FaaS` instead of a local one. Also delegate all other server functionalities to 3rd party APIs and access them via fetch/axios.  Hence omit the server completely.

Emprically,  
**Type B = Type A + Serverless backend functions**

You can use a combination of any PaaS/CDN++ and any build/CI tool, although some CDN++s have inbuilt build tools like Github pages has Jekyll. But you can override that with custom CI pipelines using tools like `Jenkins` *(which will override Github Actions)* or better by locally building the app using your favourite build-tool before pushing your build to the CDN++.

Let's now compare some popular CDN++s :

Github pages   
Codeberg pages    
Gitlab pages   
Heroku pages    
Cloudfare  
Firebase  
Vercel  
Netlify  

Refer [here](https://jekyllrb.com/docs/deployment/third-party/) or [here](https://akashrajpurohit.com/blog/top-free-services-to-deploy-full-stack-applications/) for an expanded list of static build tools.

- **FEW INTERESTING POINTS**

  Github pages and Codeberg pages are exclusively Type A since have no serverless functions supported. All the other CDN++s support proprietary serverless functions to interact with 3rd party APIs and Headless CMSs (aka cloud databases), effectively fulfilling all backend functionalities (Type B).

  GitHub Pages only deploys from GitHub repositories, while others support multiple Git providers (GitLab, Bitbucket)

  GitHub Pages has native support for Jekyll. For others, you need to specify the build tools during setup as well as in a package.json file. That's why beginners often prefer a Github pages blog.

  For Type B SSG sites, you can use any cloud database like Firestore, but `headless CMSs` are fast gaining popularity. CMSs are basically GUIs having drag-and-drop feature to create websites. Coding is done under the hood. The most famous CMS was Wordpress which was a [SSR (Traditional LAMP Stack)](#1-ssr-traditional-lamp-stack) site. It had a database and server business logic (CRUD) beneath a thin layer of UI. What if we could dispense with the UI (frontend) and only access the database in GUI mode. This would be called a Headless CMS. Examples of this include `Contentful, Sanity, Strapi and Ghost`.















































# IV. DEPLOYMENT 

After local testing, the key to deploying your app for public use is that you'll have to run both frontend and backend applications simultaneously, where the entire `application flow` from user input to backend processing back to the user input happens seamlessly.

You can use tools like `ngrok` to expose your local server to the internet during testing. But Deployment or Hosting your App is crucial to transition from Development to Production Mode.

## Frontend

### Firebase

For Firebase hosting, cd to your `/frontend` folder in the powershell and run these commands : 

```js
// Install the Firebase CLI globally using npm:
1. npm install -g firebase-tools

//Login to Firebase
2. firebase login

// Initialize Firebase in Your Project
3. firebase init
/*During the initialization process:
- Select "Hosting: Configure files for Firebase Hosting and (optionally) set up GitHub Action deploys" using the arrow keys and press Space to select.
- Choose your Firebase project from the list.
- Set your public directory to build (this is where React builds the production files).
- Configure it as a single-page app (rewrite all URLs to index.html).
Do not set up automatic builds and deploys with GitHub for now.*/

//Create a production Build of your React App
4. npm run build

//Deploy your app to Firebase Hosting
5. firebase deploy --only hosting
//This command uploads the contents of the build directory to Firebase Hosting
```

After deployment, Firebase will provide a hosting URL. Open this URL in your browser to verify that your app is live.

If you don't want to deploy to the final app URL but to a preview URL for testing and sharing, you need to replace Step 5 above with `firebase:hosting:channel deploy channel-id` as mentioned [here](https://firebase.google.com/docs/hosting/test-preview-deploy).



## Backend

### Render

1. Push your code into a public Git repository (eg. Github)
```bash
# Initialize git 
git init

# Add all files
git add .

# Make your first commit
git commit -m "Initial commit"

# Rename current branch to main if needed
git branch -M main

#Add the remote repository
git remote add origin https://github.com/galaxyeagle/backend.git

# Push it
git push -u origin main
```
2. Go to the Render Dahboard webpage and click on the "New" button and select "Web Service".
3. Select your Git provider (e.g., GitHub) and connect your repository to Render.
4. Then a configuration page will open. Enter the build (npm install) and start (npm run dev) commands of your Express App there.
5. Add any necessary environment variables in the Render dashboard under the "Environment" section. ( nb. Render ensures that these variables are only accessible to your application at runtime and are not visible to the public since they're not in the sourcecode.)
6. Click on "Create Web Service" to start the deployment process.
Render will pull your code from the repository, build it, and deploy it.
7. After deployment, Render will provide a hosting URL. Open this URL in your browser to verify that your app is live.

Render supports auto-deploy from your Git repository. So any changes pushed to the connected Github branch will trigger a new deployment.


# V. HOSTING 

Note that till now for simplicity I made you believe that app dev is a 4 step process:

Code - Build - Render - Deploy 

By using SSG sites, we essentially combined 'Build' and 'Render' stages making it a 3 step process:

Code - Build/Render -Deploy.

But now I'll tell its actually a 5 step process:

Code - Build - Render - Deploy - Host

Yes we talked a lot about serverlessness in SSG. But eventually your final app/site has to be hosted at a `domain url` on a public facing `server` running 24x7. All CDN++s/PaaS automatically used CDNs to host the deployed site and a CDN is a modern form of managed cloud server. Plus they gave a free domain url like `*vercel.app` or `*pages.dev` (cloudfare pages) or `*github.io`. So you didn't feel it. But once your app scales (eg in no. of monthly users) beyond your free tier, these CDN++s will start increasing their usage fees exponentially.

Thus some technical people prefer to use a completely free CDN++ like `Coolify` which provides all the auto-build/CI & serverless functions like other CDN++s but no hosting server. Thus you can use your own home server (most people don't have it) or rent a `VPS server` from `Digital Ocean` or `Hetzner` or `Linode` or `AWS EC2`, at a fixed cost (say $5/month).

(Fun fact: You can self host third party apps also like your Gmail client or any website eg. amazon.com if you have enough server resources !)

**Below is a small tutorial to use Coolify with Digital Ocean.** 

1. **`CONFIGURING DIGITALOCEAN`:**

- Sign up at DigitalOcean.
- Create a Droplet (their term for a server):
 - Choose Ubuntu 22.04 OS.
 - Select the $5/month plan (1GB RAM, 25GB SSD).
- Add your SSH key (optional but recommended).
- Note the server’s IP address (e.g., 123.456.78.90).

2. **`INSTALL COOLIFY ON YOUR SERVER`**

    Coolify runs in a Docker container on your server. Think of Docker as a "virtual machine light" that isolates `built` apps into `virtual containers` on the server, effectively partitioning the server. Note that this virtualisation allows isolated containers and is ≠ shared servers that was earlier used eg in Wordpress. 

    Commands to Run (via SSH):

    ```bash
    # 1. Connect to your server (replace 123.456.78.90 with your IP)
    ssh root@123.456.78.90

    # 2. Install Docker (if not preinstalled)
    curl -fsSL https://get.docker.com | sh

    # 3. Install Coolify
    docker run -d --name coolify \
      -v /var/run/docker.sock:/var/run/docker.sock \
      -v coolify-data:/data \
      -p 3000:3000 \
      ghcr.io/coollabsio/coolify:latest
    ```

    Now, Coolify’s dashboard is accessible at http://123.456.78.90:3000.

3. **`SET UP COOLIFY (LIKE VERCEL’S DASHBOARD)`**

- Open http://123.456.78.90:3000 in your browser.
- Create an admin account.
- Connect your GitHub/GitLab account (for automatic deployments).
- Add your server to Coolify (it auto-detects the Docker environment).

4. **`DEPLOY YOUR REACT APP`**

    Let’s assume your React app is in a GitHub repo: https://github.com/yourusername/my-react-app.

    *In Coolify’s Dashboard:*

  - Click Create New Project.
  - Choose GitHub > Select your repo my-react-app.
  - Configure settings:
  - Build Command: npm run build
  - Publish Directory: build
  - Port: 80 (for HTTP)
  - Click Deploy.

    *Coolify will:*

  - Clone your repo.
  - Run npm install and npm run build.
  - Serve the build folder on your server’s port 80.

5. **`ACCESS YOUR APP`**

    Your app is now live at http://123.456.78.90 (no port needed since it’s on port 80).

    *To use a custom domain (e.g., yourblog.com):*

  - Buy a domain from Namecheap/Cloudflare.
  - Point the domain’s DNS to your server’s IP.
  - In Coolify, add the domain to your project’s settings. It auto-configures HTTPS via Let’s Encrypt.

Remember that self-hosting requires that you take the pain of regularly maintaining and updating your server container eg. firewalls, SSL certificates, etc. A service like `Let's Encrypt` allows you to do that.

From the pov of the server provider, he has to manage several clients ie. several built app containers, judiciously on the same server space. So he might scale up the compute of one container and scale down another based on requirement. He would usually use a software like `Kubernetes` to automate this multi-container management.