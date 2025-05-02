# A. Reference tutorials for React 

To revise the JS needed to learn React, you can refer to [this video](https://www.youtube.com/watch?v=m55PTVUrlnA). React is a JS Frontend Library specifically designed for creating UIs. To start with React, you can refer to [Mosh's video](https://www.youtube.com/watch?v=SqcY0GlETPk). I think the best comprehensive tutorial on React is [Asabeneh's 30 days React course](https://github.com/Asabeneh/30-Days-Of-React/tree/master). 

N.B.: If you're working on VSCode, I'd recommend installing the `Prettier` extension for formatting & indentation as the long codes can becoming overwhelming to the eye ! After installing, go to File > Preferences > Settings, search for "formatter" and select "Prettier" as the default formatter. 

# B. Some updates on Asabeneh's tutorials

* Asabeneh's tutorial is from 2020, with many PRs pending & in his Chapter 3, he explains using the build tool `CRA (Create-React-App)` to instantiate your app (ie. create a boilerplate). However CRA was deprecated in 2023 & I faced dependency issues as well while installing it. So better use the `Vite` tool, which is a NodeJS package. N.B. unlike CRA, Vite is not a globally installed tool, so you need to run the installation command within each new project directory to set it up for that project. So after installing the latest version of Node (npm installs alongwith it) from the NodeJS website, go to your project folder and run the following commands in sequence in your VSCode Powershell:
    
    `npm create vite@latest 30daysofreact --template-react`  // 'npm create' is a command which scaffolds a project folder with a desired package (vite here) features. This also adds the necessary devDependencies in the package.json file which the next two commands help install.   
    `npm install`  // installs dependencies as mentioned in package.json   
    `npm run dev`  // starts the development server as defined in the package.json scripts. It typically runs the Vite development server, allowing you to view your app in the browser
    
    And boom ! Your default React App named `30daysofreact` will be served at `localhost:5173`. Now of course a boilerplate app was scaffolded for you by Vite. You can strip off the inessentials and start making your own app by following [this video](https://www.youtube.com/watch?v=RbZyQWOEmD0). Ensure that the `type="module"` attribute in the script tag in your index.html file is present for succesful rendering.  
    You can also try Vite online on [Stackblitz](https://vite.new/) or [playcode.io](https://playcode.io/) if you don't want to install anything on your local machine :) As you may notice, the no. of project setup files is also lesser there.

    Some smart people may want to run their Vite development server without installing all the Vite dependencies like this:

    ```
    npm create vite@latest 30daysofreact --template react
    npx vite
    ```
    But this runs the vite server on the go (using npx) without installing dependencies and may show error.

    If you observe, one of the files in the boilerplate app is `package.json`. This is a vital file and from now on whichever package you install in your folder will be added to the `dependencies` part of this file. You can see that `vite` is already added there. By the way, this is a Vite app, but even if it's a simpler Node project, you can create `package.json` & other basic files using `npm init -y` command in the terminal.

    <br />

* The traditional way of rendering an `App` component in a file `index.jsx` through a `root` div element of a file `index.html` is mentioned in Asabeneh's tutorial as follows:  
`ReactDom.render(App, root)`  in the index.jsx file.  
However [after React 18](https://www.syncfusion.com/blogs/post/everything-you-should-know-about-react-18), you first need to create a `root` object from ReactDom using `createRoot` method, and then use the `render` method on that, as follows:  
    ```js
    import * as ReactDOM from 'react-dom';
    import App from 'App';
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App tab="home" />);
    ```
Some advanced users use the following code:

```jsx
root.render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
);
```

Thus the `<App />` component and all its descendants will be checked according to the rules enforced by `<React.StrictMode>`. This is useful for catching potential issues early in the development process.

* In Chap 8 on States, Asabeneh uses Class based components, whereas functional components are the fad today !

# C. Key Concepts

## 1. Components 

Components in React are const variables (Chap 2 Asabeneh) or JavaScript functions (Chap 4 Asabeneh), that `return` a JSX.  

  Eg.  

  `const jsx = <tag> Content </tag>`, 

  or  

  ```js
  const ComponentName = () => {
    return jsx
  }
  ```

  Functional components are preferred for a variety of reasons, eg. enabling props. (Further, the introduction of `hooks` in React in 2018 allowed functional components to be omnipotent). As the name suggests, these are functions that take some arguments as input and return a JSX output. The input to a traditional JS function is arguments. The input to a React component similarly is Props !

  As you can see, the arrow syntax of functional components also has a declaration resembling const variables ! Even objects declaration have a similar syntax. 



## 2. Multiple Components

  You can use several .jsx files in your project, each having a component. For eg. the component from a child .js file can be exported using `export default <component-name>` and can be `imported` at the top of the parent js file and then included within the parent js file's component using `<component-name />`.

  If multiple components say `<Header />, <Body />, <Footer />` are present within the same js file, they can be combined into a container component usually called `<App />` which is what is finally rendered through the root div, using:

  ```js
  const app = (
    <div>
      {header}
      {main}
      {footer}
    </div>
  )
  ```

  If you refer to Chap 5 Asabeneh on props, you will see that the container component `<App />` **returns** its child component, while simultaneously passing attributes to it as `props`. Now `props` is an object and just like any other object, it can be destructured within the child component enabling shorter code ! 

## 3. Destructuring

  Although destructuring is a general JS or OOP concept, the above application for props is super important. So lets understand this concept. Consider a `person` object passed as a prop, let it be as follows:

  ```js
  const person = {
    firstName: "Lindsay",
    lastName: "Criswell",
    city: "NYC"
  }
  ```

  Then 

  `const { f, l, c } = person;`

  is equivalent to 

  ```js
  const f = person.firstName
  const l = person.lastName
  const c = person.city
  ```

  This is destructuring. There are other ways of destructuring as well mentioned in Asabeneh's Chap 5.

## 4. Virtual DOM

Unlike Vanilla JS, in React you cannot manipulate the DOM directly. React components re-render each time their state or props change. If you directly manipulate the DOM (eg., using `document.getElementById` and `innerHTML`), React is unaware of these changes. When React re-renders, it will overwrite the DOM with the Virtual DOM's current state, effectively undoing any manual changes you made. So in React, the Virtual DOM is the key mechanism that determines how updates are applied to the actual DOM. React uses its state management (`useState`) and lifecycle (`useEffect`) hooks.

  ## 5. States in Functional Components

Refer to [this video](https://www.youtube.com/watch?v=p7wZ_4sWZ4s) for an introduction to React states. Notice that the `useState` hook is used in this example. React hooks are tools to track & modify features like `state` & `side effects` in React. 

### i. <ins>UseState hook</ins>

The useState hook is used to add state to functional components. Its syntax is:

```js
const [state, setState] = useState(initialValue);
```
For eg.  
`const [count, setCount] = useState(0);` gives 0 as the initial value of the 1st argument (i.e. count) while the 2nd argument `setCount` sets the value of the 1st argument `count` based on the passed args , for eg. `setCount(count + 1)`.

A state can be a value, an image or even the theme of a webpage. So for clarity on changing states using the useState hook, refer to the following 3 code examples :

1. [Counter component](https://pastebin.com/5AY74WNE)  
2. [Animal Switcher](https://pastebin.com/7YcV5m44)  
3. [Theme toggler](https://pastebin.com/pPS9c0aB)  

* **A note on CSS Styles**

Note that the Theme Toggler example uses the `useState` hook as well a custom-made `useTheme` hook. It also uses *Dynamic Style Injection* to create a const styles variable and injecting it as the innertext of a created `<style>` element. Alternatively the styles could be saved in a separate `style.css` file and imported within `App.js` using `import ./style.css`. For individual elements, inline styles can also be used eg. 

```js
const Button = () => (
<button style={{ background: 'blue', color: 'white' }}>
    Click me
</button>
)
```

### ii. <ins>UseEffect hook</ins>

The useEffect hook is used to perform side effects in functional components, such as fetching data, updating the DOM, or setting up subscriptions. Its syntax is:
```js
useEffect(effectFunction, dependencyArray);
```
The `effectFunction` that contains the side effect code you want to execute.  
The `dependencyArray` is an array of dependencies. It tells React when to re-run the effect.
If omitted, the effect runs after every render.
If an empty array ([]) is passed, the effect runs only once after the component mounts. If the array has any dependency variables (e.g., [count]), React watches those variables. If any of them change, React re-runs the effect. Few examples are:

```js
# 1
useEffect(() => {
console.log("Component mounted");
}, []); // Runs only once when the component mounts
```
and
```js
# 2
useEffect(() => {
console.log("Count changed:", count);
}, [count]); // Runs whenever `count` changes
```




## 6. Context API

In deeply nested components (which is quite common in React), manually passing props through every level of the component tree can be tedious. Sometimes during this prop threading, data is passed down from the `ParentComponent` to the `GrandChild component`, even when they are not needed by the `ChildComponent`! React Context makes a value (number, object, state, etc.) globally available to all children components. Practically, that value is a `state` (eg. theme, cart, etc.) that you need to manage depending on the UI user's actions. Note that some people also use Redux as the state management tool instead of Context API. 

But returning back, say you wish to manage the `theme` state globally across a website.   

First, create a file `// src/context/ThemeContext.js` and follow **steps 1-3**:

1. **Create an empty context container `ThemeContext`**(through createContext() method) 

    Eg.:

    ```js
    import { createContext } from 'react';

    const ThemeContext = createContext();
    ```

-------------
    

2.  **Define a Provider Component say `ThemeProvider`**, accepting `{children}` as props. Within the definition of this functional component, there are 3 main parts:

    i- Initialize theme State using `useState` hook  
    ii- Define a function to change theme state say `toggleTheme`  
    iii- Install `theme` & `toggleTheme` from i- & ii- above as the `value` prop of the `ThemeContext` container using the `.Provider` class. Then wrap `{children}` components with this `ThemeContext.Provider`. The code is as as below:  
 

    ```js
      export const ThemeProvider = ({ children }) => {
      const [theme, setTheme] = useState('light');

      const toggleTheme = () => {
        setTheme((prevTheme) => (prevTheme === 'light' ? 'dark' : 'light'));
      };

      return (
        <ThemeContext.Provider value={{ theme, toggleTheme }}>
          {children}
        </ThemeContext.Provider>
      );
    };
    ```

This `ThemeProvider` component is the most important component that will be imported in `App.jsx` in Step 4.

-----------------------

3. **Create a Custom Hook say `useTheme` for consuming context**(through useContext() method)
```js
const useTheme = () => {
  return useContext(ThemeContext);
};
```
----------------

Then add **Steps 4-5** in your `App.jsx` file:

4. Necessary imports of libraries, components, etc. Eg.
    ```js
    import { ThemeProvider } from "./ThemeContext";
    ```
------

5. Wrap your returned children content inside the `<ThemeProvider>` component. Eg.
    ```js
    const App = () => {
        return (
            <ThemeProvider>
            <div>
                <h1>Themed App</h1>
                .......
            </div>
            </ThemeProvider>
        );
    };

    export default App;
    ```
---------

6. Now you can consume the context to globally change the `theme` state from anywhere in the application using the `useTheme` custom hook created in Step 3. Eg.
```js
import useTheme from "./ThemeContext";

const { theme, toggleTheme } = useTheme();
  return (
    <div>
      <p>Current Theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
```
------

As you can observe, the `ThemeProvider` component contains all the methods the client needs to play with your Context through the UI as per your code in `App.jsx`. This is the pivotal concept.

You can use multiple contexts in the same app (eg. theme, cart, etc.) Your `App.jsx` should have two Components: `AppContent` (which uses those contexts) and `App` (which provides those contexts by wrapping `App` with both providers).

eg.
```js
// Wrapper App component with providers
const App = () => {
  return (
    <ThemeProvider>
      <CartProvider>
        <AppContent />
      </CartProvider>
    </ThemeProvider>
  );
};
```


To apply your knowledge, the complete source code for 2 exciting projects using Context API are:
1. [Theme Toggler](https://github.com/ShubhDobriyal/react-context-theme-toggler)
2. [Shopping Cart](https://programwithjayanth.com/notes/react/cart-functionality-in-react/)

While in the Theme Toggler example you create a toggleTheme method, in the Cart example you usually need to define multiple methods like addToCart, removeFromCart, clearCart, etc. within the `CartProvider` component.


## 7. Routing

In the latest React Router v6, `<Switch>` wrapper is replaced by `<Routes>`. The complete nesting structure to route ceratin paths to certain Components is as follows: 

```js
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route exact path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
      </Routes>
    </BrowserRouter>
  );
}
```

For more detail, you can refer [here](7-Running%20and%20Deployment.md/#v-routing).

## 8. Navigation

Once routing is hardcoded, navigation bar can be made by nesting `<Link>s` within `<nav>` as follows:

```js
import { Link } from 'react-router-dom';

const Nav = () => (
  <nav>
    <Link to="/home">Home</Link>
    <Link to="/about">About</Link>
  </nav>
);

```

Note that `<nav>` wrapper is optional but much needed for navbar styling.

## 9. Dynamic Routing

Dynamic routes contain the :id param in the route definition.

# D. UI Component Libraries

If you carefully analyse, a React frontend project is nothing but components. All the UI you see on a webpage: the header/navbar, hero section, body, footer, buttons, sidebar, login page, table, info card, etc. are all but React components whose code you can actually re-use at multiple places. What if some expert could code all the most popular reusable `primitive` `components` and put it in a library for you? You guessed it right, it would be great. Such libraries are called `UI Component Libraries`. 

Examples include `Material UI (MUI)`, `Radix UI`, `Ark UI`, `Headless UI`, `Ant Design`,`Chakra UI`, etc. 

Except MUI, AntD and Chakra UI, all libraries are `headless`, i.e. they provide `unstyled` components. 

## MUI, AntD & Chakra/UI

MUI & Chakra/UI provide components styled with Emotion/CSS. While AntD provides components styled with Vanilla/CSS enhanced with the `Less` preprocessor. 

MUI provides a custom component `ThemeProvider` while AntD provides `ConfigProvider`. Both are React components that act as wrappers around your app or parts of it. They use the Context API internally to pass values (theme here) down the component tree without requiring prop drilling.

So you can create a `theme.js` file to customise existing styles and then theme your App like this:

```js
//App.js

import { ThemeProvider } from "@mui/material/styles";
import { theme } from "./theme"; // Import custom theme

export default const App = () => {
   return (
    <ThemeProvider theme={theme}>
      <div>
      ......
      </div>
    </ThemeProvider>
  );
}
```

## Styling headless libraries

You can write all the css in the framework of your choice for headless libraries. But some people have already done that for you.

```
Ark/UI + Panda/CSS = Park/UI
Radix/UI + Tailwind/CSS = Shadcn/UI
```
I am particularly interested in `shadcn/ui` due to its minimality and recent popularity. Although it pre-styles all components under the hood with tailwind/css, you can also use other frameworks like vanilla/css to enhance it. Moreover unlike MUI, its npm package doesn't install all the UI components. It only installs the boilerplate code and you can only install the components you need, on the go. Thus it's pretty light-weight. 

## Shadcn/ui

To install it, 

1. First, install tailwind/css using the latest docs as mentioned [here](3-CSS.md/#i-tailwind).

2. Refer to [this tutorial](https://ui.shadcn.com/docs/installation/vite) to install shadcn/ui in a vite project. However it assumes that you have used typescript in your project. If you instead use Javascript like me, then follow these steps:

- In your project root (same level as package.json), create a file named `jsconfig.json` with the following content:

    ```json
    {
      "compilerOptions": {
        "baseUrl": ".",
        "paths": {
          "@/*": ["./src/*"]
        }
      }
    }
    ```
    This defines the `@` `alias` to point to your src directory which will tell the shadcn CLI in the future, how to resolve imports like `@/components`.

    Then install the path module using `npm install -D path`  and update your `vite.config.js` file as follows:
    ```js
        import { defineConfig } from "vite";
    import react from "@vitejs/plugin-react";
    import tailwindcss from "@tailwindcss/vite";
    import path from "path";

    export default defineConfig({
      plugins: [react(), tailwindcss()],
      resolve: {
        alias: {
          "@": path.resolve(__dirname, "./src"),
        },
      },
    });
    ```
  The alias section above maps `@` to your src directory, matching the `jsconfig.json`.

-  Now `remotely` run the CLI hosted on shadcn's npm package using :  
 `npx shadcn@canary init`  
Since at the time of this writing (Apr 2025), Tailwind v4 is rolled out and `shadcn@latest` may not be compatible with it, so I'm using the `shadcn@canary` npm package.  
 Note that to keep the project lightweight and avoid versioning conflicts, I don't `locally` install the shadcn's npm package using say `npm install shadcn@canary`. Instead I `temporarily` download the npm library using `npx` and then execute the `CLI` binary sitting on that library using `init`. Thus `shadcn` itself is not added as a dependency in your `package.json` file, but this step helps add the dependencies required by shadcn/ui components (e.g., tailwindcss-animate, class-variance-authority, clsx, tailwind-merge) in `package.json`. 

    If the `init` command shows you a dependency warning offering you two options to proceed: `--force` or `--legacy-peer-deps`, then always choose the `latter`.

 - You’ll be prompted to configure your project in the CLI. You can fill it as :
    ```sh
    ? Would you like to use TypeScript (recommended)? No
    ? Which style would you like to use? New York (or Default, your choice)
    ? Which color would you like to use as base color? Slate (or your preference)
    ? Where is your global CSS file? src/index.css
    ? Do you want to use CSS variables for colors? Yes
    ? Are you using React Server Components? No
    ? Configure the import alias for components: @/components
    ? Configure the import alias for utils: @/lib/utils
    ```
    This creates `/components.json` and `src/lib/utils.js` files.

- Now install the newly added dependencies using `npm install`

- The `init` also adds tailwind's `@theme` directive to your `index.css` file. For the slate theme for example, the values will look like:

  ```css
  @theme {
    --background: 0 0% 100%;
    --foreground: 222.2 47.4% 11.2%;
    --muted: 210 40% 96.1%;
    --muted-foreground: 215.4 16.3% 46.9%;
    --popover: 0 0% 100%;
    --popover-foreground: 222.2 47.4% 11.2%;
    --border: 214.3 31.8% 91.4%;
    --input: 214.3 31.8% 91.4%;
    --card: 0 0% 100%;
    --card-foreground: 222.2 47.4% 11.2%;
    --primary: 222.2 47.4% 11.2%;
    --primary-foreground: 210 40% 98%;
    --secondary: 210 40% 96.1%;
    --secondary-foreground: 222.2 47.4% 11.2%;
    --accent: 210 40% 96.1%;
    --accent-foreground: 222.2 47.4% 11.2%;
    --destructive: 0 100% 50%;
    --destructive-foreground: 210 40% 98%;
    --ring: 215 20.2% 65.1%;
  }
  ```
  So ensure this structure is present.

- Now add any component (e.g., a Button) to your project:  
`npx shadcn@canary add button`  
So we fetch the CLI again via npx, add /src/components/ui/button.jsx and install @radix-ui/react-button as a dependency with --legacy-peer-deps.  
So the entire sourcecode of the button component is now copied to your project. You can also instead manually copy/paste it from the shadcn website and entirely omit this step !

- Now use that shadcn ui component. Eg. in /src/App.jsx :  
  Import using   
  `import { Button } from "@/components/ui/button";`   
  and use anywhere:  
`<Button variant="destructive" className="mt-2">Delete</Button>`

