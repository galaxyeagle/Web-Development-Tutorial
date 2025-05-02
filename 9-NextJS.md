
## Installation

In ReactJS, you might use vite/cra to set up a project, then add libraries for routing or styling. But Next.js provides a single command to create a project with everything you need. 

In React, you needed to scaffold the project using `npm create` command, and then install scaffolded dependencies using `npm install`. But in Next, you simply execute the CNA CLI using `npx` to scaffold + install (much like shadcn/ui)

```bash
npx create-next-app@latest my-next-app
```

## Basic Routing

In Reactjs, you used [React Router](4-React.md/#7-routing) to define routes in a Router component, mapping paths to components. But in Next.js there is page-based routing, and Routes are `automatically` created by adding files to the `/pages` directory (Page Based Routing). For example, `/pages/about.js` becomes `/about` route.

Similarly for App Based Routing (new), the `/app/about/page.tsx` becomes `/about` route.

## Navigation

Remember that in Reactjs, we [preferred](4-React.md/#8-navigation) to use `<Link>` from the react-router-dom instead of `<a>` for linking to internal paths because it kept track of browsing history and also enabled client-side routing, updating only the parts of the page that need to change, rather than the whole page reload. Example: 

```js
import { Link } from 'react-router-dom';

const MyComponent = () => (
    <Link to="/about">Go to About Page</Link>
);
```

Next.js also uses a similar `<Link>` but additionally pre-fetches the linked page in the background so when a user clicks the link, the page loads almost instantly.

NB. `<Link to='/about'>` and `<Link href='/about'>` do the same thing.


## Dynamic Routing

In Reactjs, you can use React Router to define an /:id route linking to a Template component. Then within Template.js, you can create a template. This routing and injection of any dynamic content (eg. from the params data extracted using useParams()) takes place during run-time at the client side. As learnt earlier, you can do SSR for nearly all content in React, but dynamic content like this one still happens in client side.

In Nextjs, you can even pre-render this dynamic content using SSG. Here's how:

- Create a file `[id].js` inside the /pages directory of your project. This will automatically do routing. This automatically creates a dynamic route `/:id`. 

If you use App Router instead of the Pages Router, then for instance when you place a `page.jsx` file in `/app/apps/[id]/`, Next.js automatically recognizes it as the component to render for any URL matching `/apps/[some-id]`. So you don't need to explicitly call the component in the React style. 

- Create a component file `Template.js` inside the /components directory of your project.

- Add 3 main parts in the [id].js file:  
    1. Define the async `getStaticPaths()` function to pre-render all possible paths localhost:5173/id.  
    2. Define the async `getStaticProps({params})` function to create some `appData` from the url params (nb. this is similar to the useParams() function in Reactjs).  
    3. Define the export default function say `DynamicPage({appData})` which returns `<Template content={appData.content} />`

- In the `Template.js` file, create your Template component like this:

```js
const Template = ({ content }) => {
  return (..use {content} here..)
}
```

So here we utilize the params in before build. But Nextjs also allows utilizing params in client-side during run time, using the `useRouter` hook. For example, if you had a URL like `/posts/1?category=tech`, then :

```js
// pages/posts/[id].js
import { useRouter } from 'next/router';

export default function Post() {
  const router = useRouter();
  const { id, category } = router.query; // Accessing both id and category

  return (
    <div>
      <h1>Blog Post</h1>
      <p>This is a blog post with ID: {id}</p>
      <p>Category: {category}</p>
    </div>
  );
}

```
Similarly you can use the Next Router to navigate to dynamic routes, which `<Link>` can't do. Eg.: 
```js
router.push(`/apps/${data.id}`);
```