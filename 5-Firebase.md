# Firebase

Now that you know how to make SPAs using React, you may wish to do 4 main things to make it usable by public users :
* Add some user functionality/interactivity to your app using a backend `Database`.
* Create an `Authentication` so that users can login to your app using their Google Account.
* Finally `host` your app to a public URL so that the world can access it.
* And get `analytics` on your app traffic.

These things require a lot of backend coding in PHP, NextJS, GoLang, SQL databases, SocketIO, etc. But Google provides a free backend-as-a-service (BaaS) platform called `Firebase` which allows you to do all the above 4 tasks with zero knowledge of the backend !! Relieved huh ? Yes let's begin🔥

------------------------

## Concepts

I would strongly suggest referring to the Firebase tutorial by [PedroTech](https://www.youtube.com/watch?v=2hR-uWjBAgw).   
[Fireship's tutorial ](https://fireship.io/lessons/firebase-quickstart/) is also good although it's a little old.  
I assume that you locally made a Single Page React App and now want to publicly host it as a `web app` openable on a browser. Once you learn this, I believe you can do other stuff on your own like hosting a complete dynamic website on Firebase, or adding iOS/Android/Flutter apps to your firebase project, etc.

Now a Firebase project is like a container for all your apps and any resources and services provisioned for the project. A Firebase project can have one or more Firebase Apps registered to it (for example, both the iOS and Android versions of an app, or both the free and paid versions of an app). The below figure explains this.

<figure style= "text-align:center; font-weight: bold; padding: 10px 0;">
<img src= "https://firebase.google.com/static/docs/projects/images/firebase-projects-hierarchy_projects-apps-resources.png?authuser=0" width= "60%" title= "Hierarchy of a Firebase Project">
<figcaption>Fig: Hierarchy of a Firebase Project</figcaption>
</figure>

----------------------------------------------
## Step-wise guide

Now follow these steps sequentially:

1. Go to the [Firebase Console](https://console.firebase.google.com/) and create a `new Firebase Project`.

2. `Add a web app` to that project and name it something.

3. Also give your app a `nickname`, link it to a `firebase hosting site` & `register` it.

4. ## Initializing Firebase in your local React App: 


    Next you need to add key details of this Firebase project into your local React app and thus link the two ! For that, create a new `firebase_config.js` file somewhere in your local app folder and create a `firebaseConfig` object as follows:
    ```js
    const firebaseConfig = {
    apiKey: "YOUR KEY",
    authDomain: "YOUR DOMAIN",
    projectId: "YOUR PROJECT ID",
    storageBucket: "YOUR STORAGE BUCKET",
    messagingSenderId: "YOUR MESSAGING SENDER ID",
    appId: "YOUR APP ID",
    };
    ```
    Make sure to replace the placeholder values with your actual Firebase project credentials, which you can find in the Firebase Console. **This is the only bridge you will ever need between your Firebase project and Local React App.**  
    Note that all this data is sensitive and you shouldn't let it be visible to others in your github repo, in case you decide to push your sourcecode to github. For that, create a file named `.env` in your local app folder, and cut-paste this data there. Also add env variables (starting with VITE_) for each data, <br> for eg. `VITE_API_KEY = YOUR KEY` and then just reference this env var in your `firebase-config.js` file like `apikey = import.meta.env.VITE_API_KEY`. Do this for other variables also. Then make sure your `.env` is ignored in the `.gitignore` file and then it won't be committed.

5. Firebase can also be considered as a library of reusable methods and classes available as an npm package. Now install this library in your local React project by opening your Project directory in the terminal and running `npm install firebase`.

6. The `firebase npm package` provides a no. of useful methods, the most important one being `initializeApp()`. Use it on the `firebaseConfig` object created in Step 4 by adding `const app = initializeApp(firebaseConfig);` to the `firebase_config.js` file. This creates an instance `app` that you can then use to access other all other Firebase services. All other firebase methods will use this `app` object from now on to create a `reference object` to access various Firebase services, for eg.  
    ```js
    const analytics = getAnalytics(app); 
    const auth = getAuth(app);  
    const db = getFirestore(app);
    const storage = getStorage(app);
    etc.
    ```
    Note that in above examples, even blank arguments would automatically assume the `app` object eg. simply `const auth = getAuth();` in line with the tree-shakable design of JS.

```
Note that Steps 4-6 collectively install the Firebase SDK and initialize Firebase in your local React App. This is most crucial for beginners. Firebase Console simplifies it by generating readymade code snippet after Step 3, which you can copy-paste in your `firebase_config.js` file. 
```

7. ## Code structure

Before proceeding further into some exciting examples of some apps utilizing specific firebase services, you should be knowing what your end product will eventually look like.

Your local React App folder would look like this:

<img src="https://i.postimg.cc/MT8ZKH0r/1.png">

<br>

As shown in the figure above, there is a `components` folder within `/src`, which contains many components. As a good practice, you can arrange each such component as `/src/components/component-name/index.js` instead of just `/src/components/component-name.js`. You can also see the nested structure of components and how one component is exported to a higher one until finally the index.js injects `<App>` component into the root of `<index.html>`.

The complete directory tree would look like:

```
your-app-name/
├── node_modules/              # Node modules (installed dependencies)
├── public/                     # Static assets
│   └── index.html              # Main HTML file
│   └── ...                     # Other static assets (images, etc.)
├── src/                        # Source code directory
│   ├── assets/                 # Assets like images, styles, etc.
│   │   └── ...
│   ├── components/             # React components
│   │   ├── ComponentOne/       # Example Component
│   │   │   └── index.js        # ComponentOne code
│   │   │   └── ComponentOne.module.css   # Component specific styles (CSS modules)
│   │   ├── ComponentTwo/
│   │   │   └── index.js
│   │   │   └── ComponentTwo.module.css
│   │   └── ...                 # Other components
│   ├── firebase-config.js      # Firebase configuration file
│   ├── App.js                  # Main application component
│   ├── index.js                # Entry point for React
│   └── ...                     # Other source files (e.g., utils, services)
├── .gitignore                  # Specifies intentionally untracked files that Git should ignore
├── package.json                # Project metadata and dependencies
├── README.md                   # Project description and instructions
├── vite.config.js              # Vite configuration file
└── ...                         # Other configuration files (e.g., .eslintrc.js, .prettierrc.js)

```

Note that each component has its own directory with an index.js file, so instead of importing a component like
```
import MyComponent from './components/MyComponent.js';
```
You can now import it like this:

```
import MyComponent from './components/MyComponent';
```
<br>
Also note that each component .js file has the following general structure:

<br>

<img src="https://i.postimg.cc/XNtWVpVK/2.png">

`import React from 'react'` is a common import in all component files.Other imports depend on the nature of `fire-service` utilised. When we learn to fetch data from firestore database [below](#b-reading-data-from-firestore), you will see the code execution of thr above component structure. As would see that for modularity, it's better to divide the component into 2 components - a `fire-service.js` file and a `fire-service-call.js` file.

8. ## Firestore Database
Cloud Firestore is a non-relational/NoSQL database which doesn't have tables with rows & columns, rather has `collections` having JSON object-like `documents` which store the data in `key-value pairs`. Such a NoSQL database works great with Frontend frameworks like React. There are 4 basic things you'd like to do with a database: Create, Read, Update & Delete or `CRUD`. You do so by creating reusable async JS functions (not functional components) for each and then calling those functions for doing CRUD when you wish to, as evident from the earlier figure.

You can refer to [this](https://www.youtube.com/watch?v=LCvBPsuHe6g&t=778s) tutorial for a hands-on overview on firestore, although I shall cover them as below.

### Creating a new database

Go to the Firestore webpage, create a collection say `notes` and manually add some initial documents to it.


### Initializing Firestore in your local React App:

For this, add the following in your `firebase_config.js` file:
```js
// Import Firestore module
import { getFirestore, collection } from 'firebase/firestore';

// Initialize Firestore
const db = getFirestore(app); 

// Create a reference to your collection
const notesCollectionRef = collection(db, 'notes'); 
```

From here on, you'll be using this `notesCollectionRef` object for all CRUD operations.

9. # Utilizing Firebase services in our app

Lets first absorb the algorithmic logic of using various firebase services.

## A. Login using Firebase Authentication Service

1. Create a `login component` say at `/src/components/Login/index.js`.
2. In that component file, `import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword } from "firebase/auth";`
3. Create an auth object: `const auth = getAuth();` to be used in step 4.
4. If Login button is clicked by the user, then sign him in with `signInWithEmailAndPassword(auth, email, password)`. If Register button is clicked, then create a new user with `createUserWithEmailAndPassword(auth, email, password)`.


## B. Reading Data from Firestore

1. Create a `./src/firestore-services/read.js` file at a suitable location.
2. In `read.js`, import necessary firestore functions:

```js
import { collection, getDocs, query } from "firebase/firestore";
import { db } from "../~/firestore-config; // Ensure `db` is your Firestore instance.
```

3. In `read.js`, create an async event handler function `fetchEmployees()` which reads all documents in your firestore collection and maps them into an array list.

```js
import { collection, getDocs, query } from "firebase/firestore";
import { db } from "../config/firebase-config";

export const fetchEmployees = async () => {
  
    const employeesCollection = collection(db, "employees");
    const employeesSnapshot = await getDocs(employeesCollection);
    const employeesList = employeesSnapshot.docs.map(doc => ({
      id: doc.id,
      ...doc.data(),
    }));
    return employeesList;  // Returns the employee list array
  
};
``` 
4. Create a new file `./src/firestore-services-call/readCall.js` and call the `fetchEmployees()` component when needed (e.g., on component mount within a useEffect hook). Eg.:

```js
import { useEffect, useState } from 'react';
import { fetchEmployees } from '../services/employeeService';

const readCall = () => {
  const [employees, setEmployees] = useState([]);

  useEffect(() => {
    const getEmployees = async () => {
        const data = await fetchEmployees();
        setEmployees(data);
    };

    getEmployees();
  }, []);

  return (
    <div>
      {employees.map(employee => (
        <div key={employee.id}>
          {/* Render your employee data here */}
          <p>{employee.name}</p>
        </div>
      ))}
    </div>
  );
};

export default readCall;
```
Notice in the above code that you can't directly use an async function in useEffect because it returns a Promise, which useEffect doesn't know how to handle. Hence we _both_ define the async function `getEmployees()` as well as call it within `useEffect`. This keeps the useEffect function synchronous while allowing you to perform asynchronous operations.


## C. Adding Data to Firestore

1. Import Firestore functions `collection` and `addDoc` from firebase/firestore
2. async event handler function to add data:
```js
const addEmployee = async (newEmployee) => {
  try {
    await addDoc(collection(db, "employees"), newEmployee);
    console.log("Employee added successfully!");
  } catch (error) {
    console.error("Error adding employee: ", error);
  }
};
```
3. Call `addEmployee` with the new data object (e.g., { firstName, lastName, email, salary, date }) when the user submits a form. You may use onclick() attribute for that.


## D. Editing/Updating Data in Firestore

1. Import Firestore functions `doc` and `updateDoc` from firebase/firestore.
2. Create an async event handler function to edit data. Therein you can use the `doc` function to reference the specific document you want to update (requires the document ID) and `updateDoc` to update the fields of the document.
```js
const editEmployee = async (id, updatedData) => {
  try {
    const employeeDoc = doc(db, "employees", id);
    await updateDoc(employeeDoc, updatedData);
    console.log("Employee updated successfully!");
  } catch (error) {
    console.error("Error updating employee: ", error);
  }
};
```
3. Call editEmployee with the document ID and the updated data object (eg., { firstName, lastName, email, salary, date }) when the user submits an edit form.









