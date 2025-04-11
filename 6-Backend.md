<style>
  h1 { font-size: 3em; }
  h2 { font-size: 2.5em; }
  h3 { font-size: 2em; }
  h4 { font-size: 1.5em; }
  h5 { font-size: 1.3em; }
  h6 { font-size: 1.2em; }
</style>



# Concept of Backend Development

If you started from frontend development, `Firebase (serverless backend)` is great for authentication, database, and cloud functions. But tasks like payments require a more controlled backend due to security reasons. Hence our local app folder would now look like this:

<img src="https://i.imgur.com/S1Kxh0A.png" width="50%" height="auto" style="display: block; margin: 5% auto;" >

The `frontend directory` has a `React app` and the `backend directory` has an `Express app`/Web Server. Together they make up your complete application.

So we'll make a `web server` in the backend which has two parts:

1. Backend resources eg. databases

2. The logic code, which 

    *a*.) interacts with the **resource/database** through CRUD operations

    *b*.) interacts with the **frontend** code by `listening` to `requests` by the `client` on some `port` on the server, and then sending a `response` to that request.

    This logic code performing the above 2 interactions is called `REST API`.

The below diagram represents API as an overlapping part and connector of frontend and backend.

```c
// Frontend = Client-side code + Fetch API
// API = Fetch API (Request) + REST API (Response)

[Frontend Client]
       | ^
Request|+| Response = [Fetch API] 
       v |
   [REST API]
      |
      |  
      v
[Backend Resource]

// Backend server = REST + Backend Resource
```
 A casual user of your application needn't know how APIs work. 
 
 Who would learn using APIs :

  * The backend developer would use `REST API`.
  * The frontend developer fetching backend resources would use `Fetch API`.
  * The frontend developer fetching data or business logic sitting in the backend server/database of 3rd party applications (eg. Payt. Gateways, LLMs, Google Maps, etc.) using their public APIs, to utilize in his own application, would use `Fetch API`.
  
  So let's begin by setting up the web server and then within that we'll design a REST API layer and then make the database resource.

# 1. Setting up a Backend Web Server

Refer to [this](https://www.youtube.com/watch?v=UejzwLB0en0) video for a tutorial.  
`ExpressJS` is a popular Node.js framework for making backend REST APIs. To install it, cd to `/backend` directory and run. 

```js
npm init -y
npm install express 
``` 

Then create an `index.js` file (which will act as your server) as follows:

```jsx
const express = require('express');

const app = express();   
// creates an instance of the Express application and assigns it to an object called app which will be our main tool for defining routes, handling requests, etc.

app.listen(3000); // server starts at port 3000 of localhost

```

Then run this server using `node index.js` and open http://localhost:3000 in the browser.

As of now, your `/backend` directory tree looks like this:

```
/backend
│── index.js
│── package.json
```


# 2. APIs - Writing & Using them

As discussed there are 2 main aspects of an API: `request (by client)` and `response (by backend)`. 

## 2A. Request (Fetch API)

Learning about CRUD requests is the first step. But sending requests is done by the frontend client. So ideally this should not be in this `Backend` Chapter. In fact, other than sending requests to your own backend, you can send requests to the backend of popular 3rd party apps like Google Maps and make exciting projects ! 

---
**Sidenote for advanced readers:**

*But there's another reason why `Requests` is in the Backend chapter. Whenever your CRUD request has some confidential personal information, putting it in the frontend code makes it prone to hackers who can use say browser dev tools to take that personal info and send it to their hacky REST APIs instead ! These include sending CRUD requests to 3rd party Payments API (eg. Stripe), Location Tracker APIs, etc. So what you'd do is to send your non-confidential CRUD request to the REST API in you own application's backend, and then forward that request to the 3rd party API from the backend after adding confidential info. But there's an issue. Backend is coded in Express, and using `fetch()` in backend entails importing npm libraries and complicates the fetch() syntax, like a Fetch API inside a REST API ! So almost all famous confidential 3rd part APIs provide their SDK as npm libraries which you can install in your backend and then initialize an SDK instance with your confidential info (as environment variables for added security) and then use CRUD methods of that SDK on the confidentialized instance to forward the frontend `request` to the 3rd party API. The 3rd part API's response can then be sent to the frontend (using res.send) to create a personalized modal for you (eg payment dialog box, or location tracker map window, etc.). Refer to the [Payment example](./8-Applications.md/#1-add-payment-page-to-your-web-app) in the next chapter for a complete illustration.*

---

Each client request to the server contains (1) `endpoints` (to identify the resource) and (2) standard `HTTP methods (eg. GET, POST, etc.)`.   

Examples :  
* Twitter API uses `GET /tweets` to fetch tweets,   
* Weather Service API returns JSON data via `GET /weather?city=London`,  
* A Payment processing API would use `POST /orders` to create an order and `GET /orders/456` to retrieve its status.

Clients can interact with your server by sending `CRUD` requests (Create, Read, Update, Delete) on resources. Note that this `resource` is usually in JSON or XML format. All CRUD requests are possible using `HTTP methods`:

| **Request** | **HTTP Method** |
|---------------|-----------------|
| Create        | POST            |
| Read          | GET             |
| Update        | PUT             |
| Delete        | DELETE          |


There are many components of HTTP Requests.

### Key Components of an HTTP Request

#### a. Query Params

These are used for filtering, sorting, or providing additional context.They're part of the URL, following the "?" character, arranged in key-value pairs like NoSQL. It's visible to end users. 

Eg. in `GET /https://api.example.com/products?category=electronics&sort=price&order=asc`, we have the params as:

| **Key** | **Value** |
|---------------|-----------------|
| category        | electronics            |
| sort          | price             |
| order        | asc             |
|


(Note that you can send a GET request to a url by simply entering the URL in your browser's address bar alongwith query params written in the "serialized/form-encoded" format after the `?` as told above)

#### b. Request Body

This contains the main request payload and is not part of the URL. 

`POST` and `PUT` requests submit JSON data in request body like this:

```
{
  "username": "john_doe",
  "email": "john@example.com",
  "preferences": {
    "theme": "dark",
    "notifications": true
  }
}
```

There is no request body for a `GET` request, but you can use query parameters to help specify the resource you want data on (e.g., in `GET /{{base_url}}/info?id=1`, we have id=1), where base_url is a variable where you can store the api url (Note that you can encode your API endpoint as a variable in Postman).

#### c. Headers

They contain some metadata about the request being sent. It's hidden from URL and usually optional. However when you're sending a POST/PUT request with json body, you should atleast include this header in the request :
`Content-Type: application/json`


### 2A-i. FETCH API

```
Fetch API is the standard syntax to be used in your frontend code for sending HTTP requests from the frontend (client) to your backend server.
```

So let's learn the Fetch API syntax and how to use it.

Fetch API is built into the browser's JavaScript. So you can use the `fetch()` method directly in your frontend code without needing to include any external libraries. Fetch API is the successor to the now outdated XHR.

#### `2A-i-a. EXAMPLES OF CRUD REQUESTS IN FETCH API USING ASYNC FORMAT`

Before learning the Fetch syntax, let's understand async operations because all CRUD requests are async.

`Asynchronous programming` is a crucial concept in JavaScript that allows you to execute code without blocking the main thread. This is particularly important for tasks that take time to complete, such as fetching data from a server or reading files. CRUD requests are inherently asynchronous because they involve communication over the network A `promise` is the output of an async operation. The `async/await` syntax is built on top of promises and allows you to write asynchronous code that looks synchronous. Here’s how you can use it:

```js
async function performAsyncOperation() {

    const result = await myPromise; // Wait for the promise to resolve
    console.log(result); // Logs: Operation completed successfully!
  
}

// Call the async function
performAsyncOperation();
```
In this example, the `await` keyword pauses the execution of the `performAsyncOperation` function until myPromise is resolved. If the promise is fulfilled, it logs the result. A `try-catch` block is often used to catch any errors in resolution of the promise.

Now let's send a `GET` request using `fetch()` based on the above async format.

```js
async function fetchData(url) {
  
    const response = await fetch(url); // Execution pauses here until fetch completes
    const data = await response.json(); // Waits for the JSON parsing to complete
    console.log(data); // Logs the data once it's ready

}
```

`performAsyncOperation()` is replaced with `fetchData(url)`.  
`myPromise` is replaced with `fetch(url)`

Similarly you can send a `POST` request as follows:

```js
async function postData(url, data) {

     const response = await fetch(url, {
      method: 'POST', // Specify the request method
      headers: {
        'Content-Type': 'application/json', // Set the content type to JSON
      },
      body: JSON.stringify({ name: 'John Doe', age: 30 }), // Convert the JS object to a JSON string
    });


    const result = await response.json(); // Wait for the JSON parsing to complete
    console.log(result); // Logs the result once it's ready
}
```

In the POST request example, we're specifying the `method` option as 'POST', specifying json content type in `headers` as well as providing a `body` with the data to be sent in the request body.


#### `2A-i-b. THE COMPREHENSIVE GENERAL SYNTAX FOR USING fetch() :`

In most practical cases, for each functionality (say `MyComponent`) in your app (eg. BookOrder, Payments, etc.), you'll have one `MyComponent.js file` which contains `one functional component` called MyComponent which can contain several `async CRUD functions` called `handleCRUD_i` for different CRUD requests. This async CRUD function is written in `arrow format`. It is then called as an onclick event listener on `buttons` like `Pay now`, `Add to Cart`, `Download file`, etc. within the `return()` jsx section of MyComponent.js. To parse the jsx, you need to import `react` library. 

`MyComponent.js` would look as follows:

```js
//MyComponent.js

import React from 'react';

const MyComponent = (props) => {


  const handleCRUD_1 = async() => {
      const response_1 = await fetch(url_1, options_1);
      .then(response_1 => response_1.json())
      .catch(error_1 => console.error(error_1));  // Handles any errors
      ...SOME CODE TO UTILIZE response_1, eg. set some states...
    }


  const handleCRUD_2 = async() => {
      const response_2 = await fetch(url_2, options_2);
      .then(response_2 => response_2.json())
      .catch(error_2 => console.error(error_2));  // Handles any errors
      ...SOME CODE TO UTILIZE response_2...
    }
  . 
  . 
  . 
  const handleCRUD_n = async() => {
      const response_3 = await fetch(url_n, options_n);
      .then(response_3 => response_3.json())
      .catch(error_3 => console.error(error_3));  // Handles any errors
      ...SOME CODE TO UTILIZE response_3...
    }


  return (...some html code like buttons etc...);
};

export default MyComponent;
```

where `url` is the API endpoint and `options_i` may be like

```js
const options_i = {
  method: '...',
  headers: {
    'Content-Type': 'application/json'
  }
  body: JSON.stringify(data),
};
```

When `method` is not specified, GET is taken by default. Thus simply `fetch(url)` would imply a GET request. 

Note that `.then(response_1 => response_1.json())` is a Fetch API shorthand for writing `const response_1 = await response_1.json()`

Behind the scenes, the `fetch()` method returns a promise that automatically resolves to a Response object. The `then()` method is used to handle the resolved promise, and the Response object is passed as an argument to the callback function.


### 2A-ii. Postman

 Before writing any JavaScript fetch() code, you may want to quickly check if the endpoint/ backend REST API is working correctly, what data it returns, and how it responds to different parameters. For that you can use the GUI tool [Postman](https://www.youtube.com/watch?v=T79fxJzfgrM) to send CRUD requests. This is only useful for `testing APIs`, including 3rd party APIs. `Insomnia` is another GUI alternative.



### 2A-iii. cURL 

Just as Postman is a GUI tool for testing APIs, cURL is a command line tool. The syntax is `curl "api url" -X "HTTP method"` For GET request you can write `curl -G "url"` or simply `curl "url"`. 

If you have `jq` installed (a command-line JSON processor), you can pipe the output of curl to jq for a more readable JSON output: `curl "url" | jq`. You can also `redirect` the output to a bash file : `curl "url" > data.json`. Such bash scripts can be used to create cron jobs to schedule tasks that interact with your API. For example, if you need to send daily reminders to users, you can set up a cron job that triggers an API endpoint responsible for sending those reminders. This can be done by defining a cron job in your server's crontab file, specifying the frequency and the command to call your API. For instance, to run a script every day at 23:00, you would add the following line to your crontab:

`0 23 * * * /usr/bin/curl -X POST http://yourapi.com/send-reminders`

Similarly if your application needs to pull data from external APIs regularly, cron jobs can be used.

---

You can also `echo` the exit code of the last executed `curl` command (ie. `$?`). This will tell you if the `request` was succesful or not. `0` means success while a `non-zero` value means failure. 

**Flags**

Refer [here](https://reqbin.com/req/php/c-skhwmiil/curl-flags-example) for the popular cURL flags.

- `curl -v "url"` : To check for errors, you might want to add the -v (verbose) flag to the curl command.

- `curl -f "url"` : By default, curl considers a `request` successful as long as it can connect to the server, send the request, and receive any response (even an error response like 400, 401, 403, 500, etc.). So the -f flag causes curl to return a non-zero exit status if the server returns an HTTP status code of 400 or higher (4xx or 5xx errors). This gives you a more accurate picture.

- `curl -u username:password http://example.com` : Here -u flag authenticates your server with username and password (for authenticated servers)

cURL makes the request very precise. For example consider the following request in Fetch API :

```js
fetch('https://api.example.com/submit?category=tech&priority=high', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer token123'
  },
  body: JSON.stringify({
    name: 'John Doe',
    email: 'john@example.com',
    message: 'Hello world'
  })
})
.then(response => response.json())
.then(data => console.log(data))
.catch(error => console.error('Error:', error));
```
In cURL this can be written as: 

```js
curl -X POST "https://api.example.com/submit?category=tech&priority=high" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer token123" \
  -d '{
    "name": "John Doe",
    "email": "john@example.com",
    "message": "Hello world"
  }'
```

## 2B. Response (REST API)

Responses to HTTP requests from the client are done by `defining routes` in the backend. Routes defined in the REST syntax are called `REST APIs`. The structure of responses to various types of requests is usually compiled in the `API documentation` for a publicly usable RESTful API like Twitter API. 

### 2B-i. REST API

`REST` is the most popular syntax for defining routes/APIs and the one we'll  be learning. `GraphQL` is another popular syntax for this, appearing more like a database query language. 

```
So just like Fetch API is the syntax for sending CRUD requests, REST API is the syntax for listening + handling those requests and then sending reponses.
```

A `RESTful API` (sometimes just called REST API in short) is an API built using the `REST` design principles which enables communication between the `resources` (e.g., user data, products) sitting on the `backend server` that you previously made and the frontend `client` requesting those resources. 

To further appreciate, think about the following applications of RESTful API :  
* A mobile app might use a RESTful API to fetch user data from a server.
* A web application might use a RESTful API to update a database.
* IoT devices might use RESTful APIs to send sensor data to a cloud service.

( Note that ExpressJS is a framework built on JavaScript and produces Nodejs compilable code. But REST or GraphQL APIs can be built in other languages also. So competitors to ExpressJS include FAST API, Flask & Django (built upon Python), ASP/.NET (built upon C#), Golang (built upon C/C++), NestJS & Fastify (also built upon JS), Ruby/Rails and Rust. But we'll use ExpressJS throughout to create our backend server, because like Frontend's ReactJS, it is a Javascript framework, so it'll be a super easy learning experience. Plus, it's simpler than NestJS & FastifyJS.)

#### 2B-i-a. Syntax & Examples

The REST API standard format for defining a route is :

`<Server App Name>.<HTTP method name>('endpoint', (req, res) => {res.send(......);});`

or in short

`app.METHOD(PATH, HANDLER)`


As seen, the 1st argument is the endpoint or path you're defining and the 2nd argument is the callback function called `route handler`. You have to define a route handler for each and every route in your application, or else there'll be a 404 error.





Few examples of routes listening to different CRUD requests from the client are:

* When a client sends a GET request to /welcome, the server responds with a plain text message: "Welcome to our application!".

  ```jsx
  app.get('/welcome', (req, res) => {
  res.send('Welcome to our application!');
  });
  ```

*  When a client sends a POST request to /users with user data in the request body to create a new user, the server creates the user and responds with a confirmation message.

    ```jsx
    app.post('/users', (req, res) => {
      const newUser = req.body; // Assuming the client sends user data in the request body
      res.send(`User ${newUser.name} has been created successfully!`);
    });
    ```

* When a client sends a PUT request to /users/:id (e.g., /users/123) with updated data for a specific user, the server responds with a message confirming the update. 

  ```js
  app.put('/users/:id', (req, res) => {
    const userId = req.params.id; // Extract user ID from the URL
    const updatedData = req.body; // Assuming updated data is sent in the request body
    res.send(`User with ID ${userId} has been updated with new data: ${JSON.stringify(updatedData)}`);
  });
  ```

* When a client sends a DELETE request to /users/:id (e.g., /users/123), the server responds with a message confirming the deletion of the user. Any request which doesn't match a defined route will get a 404 response.

  ```js
  app.delete('/users/:id', (req, res) => {
    const userId = req.params.id; // Extract user ID from the URL
    res.send(`User with ID ${userId} has been deleted.`);
  });
  ```

Also note that in the above examples, we can extract useful data from the request using req.params,  req.body, etc.


#### 2B-i-b. Middleware

Now that you know how to make simple API routes to listen to incoming requests, it's time to learn about middleware. It sits in the server code and processes the incoming request before the route handler handles it. In simple terms, Middleware in Express is just a `function` that has access to the request (req), response (res), and the next() function, which passes control to the next middleware or route handler. The following code snippet from the server file `index.js` clarifies the concept.

```jsx
// Logger Middleware
  const loggerMiddleware = (req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} request for ${req.url}`);
  next(); // Proceed to the next middleware or route handler
};

// Authentication Middleware
  const authMiddleware = (req, res, next) => {
  // Example: This checks if the request contains a header named x-auth-token. Headers are often used to pass authentication tokens in HTTP requests.
  const authToken = req.headers['x-auth-token'];
  if (!authToken) {
    return res.status(401).json({ error: 'Unauthorized: No token provided' });
  }
  // Add additional authentication logic here (e.g., token validation)
  next(); // Proceed to the next middleware or route handler
};

// Apply middlewares globally
app.use(loggerMiddleware);
app.use(authMiddleware);

// Example Route
app.get('/', (req, res) => {
  res.send('Welcome! You are authenticated.');
});

// Start the server at port 3000, i.e. accessible at http://localhost:3000/ in the browser
const PORT = 3000;
app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`); // the app.listen function tells the server to start listening for incoming requests on port 3000. The second argument is a route-handler function that will be executed once the server is up and running. In this case, it simply logs a message to the console, letting you know that the server is ready to go.
});
```

Here, `next()` passes control to the next middleware function. If you have the `res.send(...)` function instead, the req-res cycle ends and final response is sent to the client. If there's neither of the two, the request will be left hanging. 

Also, the `app.use(path, middlewareFunction)` method is used to apply/load/mount the middleware. You can `mount` a middleware to a specific path/route. If no `path` is mentioned in its argument, it's applied globally.

As you can understand now, the middleware performs functions like logging, authentication, error handling, etc. before the route handler handles those requests.

As you would see later, Express has many `built-in middlewares` like `express.Router`, `express.static`, `express.json`, etc. which you can directly mount in your server app using the .use() method and make your server app extremely `modular`.

So till now our Web Server file (say index.js) looks like this:

<img src= "https://i.imgur.com/M6j4bYT.png"/>

and our entire `/backend` directory looks like this:

```
/backend
│── index.js
│── package.json
```

#### 2B-i-c. Routing

Refer to the standard syntax for defining routes as mentioned in [Section 2B-i-a](#2b-i-a-syntax--examples). Now you may need to define several routes on the same endpoint. For example, on the `/users` endpoint, you may want to define the following routes:

```
GET /users: Retrieve a list of users.
POST /users: Create a new user.
GET /users/:id: Retrieve a specific user by ID.
PUT /users/:id: Update a specific user by ID.
DELETE /users/:id: Delete a specific user by ID.
```

Once you start defining all of them within the `index.js` file, your index.js file code will become clumsy and blow up in length as explained in [this excellent video](https://www.youtube.com/watch?v=0Hu27PoloYw). Instead it's possible to create separate .js route files for each of them and put them all in a separate `/routes` folder as explained in the same video. Then your backend directory would look something like this:

```
/backend
│── /routes
│    ├── users.js
│    ├── orders.js
│── index.js
│── package.json
```

Here we have 2 route files: `users.js` would handle all routes related to user operations (like creating, retrieving, updating, or deleting users), while `orders.js` would manage routes related to order operations. You can make many more.

To give you an idea, the `/routes/user.js` route file handling simple GET & POST requests would look as follows:

```js
const express = require('express');
const router = express.Router();

// Sample data to simulate a database
let users = [
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
];

// Get all users
router.get('/', (req, res) => {
    res.json(users);
});

// Get a user by ID
router.get('/:id', (req, res) => {
    const userId = parseInt(req.params.id, 10);
    const user = users.find(u => u.id === userId);
    if (user) {
        res.json(user);
    } else {
        res.status(404).send('User not found');
    }
});

// Create a new user
router.post('/', (req, res) => {
    const newUser = {
        id: users.length + 1,
        name: req.body.name,
        email: req.body.email
    };
    users.push(newUser);
    res.status(201).json(newUser);
});

// Export the router
module.exports = router;
```

Within each route file, it's not proper to use `app.<HTTP method name>` to define routes, as the `app` object should be reserved for the `index.js` file. Therefore you use `express.Router()` to creates a new `router` object. You can think of it as a `mini-application` that can handle its own routes independently of the main `app` instance. You can then `mount` this Route file in the `index.js` file as a middleware using `app.use()`. Cool, isn't it. Thus Routing allows you to create modular and mountable route definitions, particularly useful for large applications.

The `index.js` file may then look as follows:

```js
// index.js

const express = require('express');
const app = express();
const port = 3000;

// Middleware to parse JSON bodies
app.use(express.json());

// Import and use the users and orders routers
const usersRouter = require('./routes/users');
const ordersRouter = require('./routes/orders')

// Use the routers for api routes
app.use('/api/users', usersRouter); 
app.use('/api/orders', ordersRouter);

// Start the server
app.listen(port, () => {
    console.log(`App is listening on port ${port}`);
});
```

The `usersRouter` is imported from `./routes/users.js` and mounted at the `/api/users` path. This means all routes defined in `users.js` will be prefixed with `/api/users`. Prefixing routes with `/api` helps in organizing API endpoints and differentiating them from other types of routes (e.g., static pages). It also allows for easy versioning of the API, such as `/api/v1/users, /api/v2/users`, etc., which is beneficial for maintaining backward compatibility as the API evolves.

#### 2B-i-d. Controllers

Using Routing, you created modular routes in different route files. However you define several routes in each route file. And each route has a route handler as the callback function. Right? So now each route file may start becoming clumsy. What's the solution? - simple - create one `controller js file` for each route file to keep it's route handlers. Then your server directory would start looking like:

```
/backend
│── /controllers
│    ├── users.js
│    ├── orders.js
│── /routes
│    ├── users.js
│    ├── orders.js
│── index.js
│── package.json
```

Again to give you an idea, the previous `/routes/user.js` file would shorten up like this:

```js
const express = require('express');
const userController = require('../controllers/users'); // Adjust the path as necessary
const router = express.Router();

// Get all users
router.get('/', userController.getAllUsers);

// Get a user by ID
router.get('/:id', userController.getUserById);

// Create a new user
router.post('/', userController.createUser);

// Export the router
module.exports = router;
```

Cool, isn't it?  
And the `/controllers/users.js` file may look like:

```js
// Sample data (an in-memory array) to simulate a database 
let users = [
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
];

// Get all users
exports.getAllUsers = (req, res) => {
    res.json(users);
};

// Get a user by ID
exports.getUserById = (req, res) => {
    const userId = parseInt(req.params.id, 10);
    const user = users.find(u => u.id === userId);
    if (user) {
        res.json(user);
    } else {
        res.status(404).send('User not found');
    }
};

// Create a new user
exports.createUser = (req, res) => {
    const newUser = {
        id: users.length + 1,
        name: req.body.name,
        email: req.body.email
    };
    users.push(newUser);
    res.status(201).json(newUser);
};
```


### 2B-ii. GraphQL API

It treats requests as queries using SQL and unlike REST, uses a single endpoint to accept multiple requests - sort of one bulky request instead of multiple smaller requests. Working details are not needed for this tutorial.


# 3. Webhooks

Webhook is a specialized Fetch API.

You hardcoded Fetch API in your client side code. So in your frontend code, you manually created an API request (via a button onclick and then hardcoding a CRUDhandler function with fetch api) to some endpoint url.

**Webhooks are customized Fetch API requests you can add within the frontend of third party apps like Stripe (called `trigger apps`) using a UI in the website of the trigger app, without any coding.** 

These API calls will of course go to some endpoint *(called webhook url or destination url)* in the backend of some app. It can be your own app's backend or **directly** the backend of a 3rd party app (called `action app`).

The latter case is possible when the Action App natively provides webhook URLs. In the former case, your backend server acts as an intermediary between Trigger and Action Apps. This is often used when the Action App doesn't natively provide webhook URLs to receive data directly from 3rd party Action Apps, and requires you to do some coding in your backend using REST and SDKs of the Action App. If you don't want to do coding in such cases, you can use friendly softwares like `Zapier` designed for non-technical people.

Thus a trigger in one app can lead to some action in another, which is the basis of web automation.

<img src="https://i.imgur.com/sfYCXMO.png">

**Example:** You can add and configure a webhook in your Stripe (`trigger app`) dashboard, by specifying triggering event (like a successful payment) and the URL of your server's endpoint (e.g., `https://yourserverdomain.com/api/webhook/stripe`). When the event occurs, Stripe frontend automatically sends an HTTP POST request to your specified endpoint with a JSON payload that includes details about the event. 

Webhook automations have tremendous applications, eg.:

- Suppose you have a GitHub repository, and you want to be notified whenever someone pushes new code to that repository. You can set up a webhook on GitHub that triggers every time there’s a push event.
- If you have an e-commerce site, you might want to send a notification to a third-party service or app when a user completes a checkout.
- You have a blog, and you want to notify a Slack channel when a new blog post is published.

Popular Trigger Apps include:

- Stripe/Razorpay
- Gmail
- Github
- Typeform
- Shopify

Popular Action Apps include:

- WhatsApp
- Twilio
- Integromat (Now Make):
- Slack
- Discord
- Webhook.site
- Trello
- Microsoft Teams

Unlike normal Fetch APIs, Webhooks usually have one way communication (webhook url doesn't send any response to trigger app). This is why webhooks are sometimes called "Reverse APIs" or "HTTP Push APIs." Instead of pulling data from the server when you need it (like with Fetch), the server pushes data to you when something interesting happens. It's sometimes called a "web callback" because your action app's endpoint is being called back by the trigger app when an event occurs.

Webhooks are the backbone of web automation. APIs send you the data when you request it. For Webhooks, you do not need to make a request. You receive the data whenever it is available.



# Connect the dots

This chapter also made you appreciate why you learnt coding your frontend in ReactJS, and not in vanilla html/css/js like beginners :). That's because React goes beyond the basic javascript interactivity `(like DOM manipulation by toggling classes on events like onclick())` and allows much more like --> database querying, and in general sending fetch/axios api requests which form the basis of interactive client data exchange (like "feedback forms"), and auth, payments, 3rd party app integrations, etc. TL;DR: It allows dynamic web apps and not just static sites built out of static html/js/css using renderers like jekyll/hugo. Once you create a project in React/Express, you can host it on 


