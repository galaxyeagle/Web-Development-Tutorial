# Why Nextjs ?

The biggest shift or difference between **React.js** and **Next.js** lies in their **core architecture and use cases**:

### **1. React.js is a UI Library, Next.js is a Full Framework**
   - **React.js** is primarily a **client-side JavaScript library** for building dynamic user interfaces. It focuses on component-based UI development but leaves routing, SSR, and other features to third-party libraries.
   - **Next.js** is a **full-fledged React framework** that extends React with built-in features like:
     - Server-Side Rendering (SSR)
     - Static Site Generation (SSG)
     - API routes (backend-like functionality)
     - File-based routing
     - Built-in optimizations (Image, Font, Script optimizations)

### **2. Rendering Approach**
   - **React.js** mostly uses **Client-Side Rendering (CSR)** by default, meaning the browser downloads a minimal HTML file and renders everything via JavaScript.
   - **Next.js** supports multiple rendering methods:
     - **SSR (Server-Side Rendering)** → Rendered on the server per request.
     - **SSG (Static Site Generation)** → Pre-rendered at build time (faster, SEO-friendly).
     - **ISR (Incremental Static Regeneration)** → Updates static content periodically.
     - **CSR (Client-Side Rendering)** → Still possible but not the default.

### **3. Routing System**
   - **React.js** requires external libraries (like `react-router-dom`) for routing.
   - **Next.js** has **file-system-based routing** (just create a file in `/pages` or `/app` in Next.js 13+).

### **4. Backend Capabilities**
   - **React.js** is frontend-only; you need a separate backend (Node.js, Express, etc.).
   - **Next.js** includes **API routes** (`/pages/api` or `/app/api`), allowing you to write backend logic directly in your app.

### **5. SEO & Performance**
   - **React (CSR)** can suffer from poor SEO since search engines may struggle with JavaScript-heavy pages.
   - **Next.js (SSR/SSG)** delivers pre-rendered HTML, improving SEO and performance.

### **When to Use Which?**
- Use **React.js** if:
  - You need a single-page application (SPA).
  - You want full control over your setup (e.g., custom Webpack config).
- Use **Next.js** if:
  - You need SEO optimization (blogs, e-commerce).
  - You want built-in routing, SSR, SSG, or API routes.
  - You're building a full-stack app with React.

### **Summary**
The biggest shift is that **Next.js takes React and adds a full-stack, production-ready framework** with server-side capabilities, whereas React alone is just the UI layer. If React is the engine, Next.js is the entire car. 🚀

# Installation

In ReactJS, you might use vite/cra to set up a project, then add libraries for routing or styling. But Next.js provides a single command to create a project with everything you need.

In React, you needed to scaffold the project using `npm create` command, and then install scaffolded dependencies using `npm install`. But in Next, you simply execute the CNA CLI using `npx` to scaffold + install (much like shadcn/ui)

```bash
npx create-next-app@latest my-next-app
```
# Routing

## Basic Routing

In Reactjs, you used [React Router](4-React.md/#7-routing) to define routes in a Router component, mapping paths to components. But in Next.js there is page-based routing, and Routes are `automatically` created by adding files to the `/pages` directory (Page Based Routing). For example, `/pages/about.js` becomes `/about` route.

Similarly for App Based Routing (new), the `/app/about/page.tsx` becomes `/about` route.

## Navigation

Remember that in Reactjs, we [preferred](4-React.md/#8-navigation) to use `<Link>` from the react-router-dom instead of `<a>` for linking to internal paths because it kept track of browsing history and also enabled client-side routing, updating only the parts of the page that need to change, rather than the whole page reload. See this example:

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

## Serverless functions

Nextjs supports two types of route files : UI & API:

1. `page.tsx`: Used for UI pages. If you want a route to render a React component (i.e., a web page), you create a page.tsx file inside a folder that represents the route segment. For example, `/app/about/page.tsx` becomes `/about` in your app.

2. `route.ts`: Used for API routes (serverless functions). If you want a route to handle API requests (like POST, GET, etc.), you create a route.ts file inside the relevant folder. For example, `/app/api/chat/route.ts` becomes `/api/chat` as an API endpoint.

Thus Nextjs serverless functions omit the need for a separate backend server.

In Nextjs, App Router’s equivalent of API routes (eg. app.post() in Expressjs) are called `Route Handlers`. You define them by `exporting` async functions named after HTTP methods (e.g., GET, POST, PUT, etc.) in a route.ts file.

Their standard format is :

```jsx
export async function HTTP-Method(req: Request) {
  // ...your logic
}
```
For example:
```tsx
// app/api/hello/route.ts

export async function GET(request: Request) {
  // You can access request.url, request.headers, etc.
  return new Response('Hello from Next.js App Router!');
}
```
The above function uses the globally available Web `Request` and `Response` APIs. It can be accessed at the /api/hello endpoint using cURL, Postman or using async/await with the Fetch API inside a Server Component like this:

```jsx
async function fetchData() {
  const response = await fetch('http://localhost:3000/api/hello');
  const data = await response.json();
  console.log(data);
}

fetchData();
````

Common Nextjs Endpoints/Route handlers you may create are:

| Use Case              | Example Code/Description                                                                 | Endpoint Example             |
|-----------------------|------------------------------------------------------------------------------------------|------------------------------|
| Hello World API       | Returns A Simple JSON Response                                                           | /api/hello                   |
| Database Query        | Fetches Data From A Database (e.g., MongoDB, PostgreSQL) And Returns It As JSON          | /api/products                |
| Authentication        | Handles User Login And JWT Token Issuance                                                | /api/login                   |
| Third-Party API Proxy | Receives A Request, Calls An External API (e.g., OpenAI, Stripe), And Returns The Result | /api/chat(like Your Example) |
| Form Submission       | Receives Form Data From The Frontend, Processes It (e.g., Sends An Email), And Responds  | /api/contact                 |


# App Router

Unlike React, Nextjs supports file-based routing. Pages Router is the traditional way and App Router the new way to do file-based routing. You have already seen previously the difference in structures of the App Router & Pages Router. You get to choose one of these at the time of creating your project using `create-next-app`.

## Fetch API Calls within async/await blocks
Pages Router is Client-heavy by default. For server tasks like `fetch api`, special data-fetching functions like `getServerSideProps` (SSR)/`getStaticProps` (SSG) respectively, i.e. export an async function called getServerSideProps/getStaticProps from a page. Any `fetch` has to be done from within them. But in App Router, by default, files in the `app/` directory are treated as `React Server Components`. This means you can export your page component itself as an `async` function and fetch data directly inside them. The server executes these functions, fetches the data, and sends the rendered HTML to the client. For interactivity possible only in client-side viz. hooks, states, you can add `'use client'` at the top of the page code.

Now you must be thinking why to run fetch api calls on the server like REST API. THey can run on the client/browser, right? Here's where Nextjs trumps over Reactjs. By running on the server, you can run it at build time (SSG) or request-time (SSR) which makes it fast + eleiminatesthe need of intermediate SDKs within Expressjs endpoints, as Nextjs guarantees there’s no risk of exposing secrets or private logic to the client.

To ensure SSR happens instead of SSG, you can simply add a `cache` propert like this:

```js
const res = await fetch('https://api.example.com/user', { cache: 'no-store' });
```

Also by adding a `revalidate` parameter just before the async block, you can cause ISR (Incremental Static Regeneration).

```js
export const revalidate = 3600; // Revalidate every hour
```

Thus the page is statically generated, but Next.js will revalidate and update the page every hour

## Route Handlers as frontend substitutes of REST APIs

Earlier you would have defined a Nodejs endpoint like

```js
//backend/index.js
app.post('/api/users', (req, res) => {
  const { name } = req.body;
  res.json({ name });
});
```
In Nextjs the corresponding Route Handler would look like

```js
// app/api/users/route.ts
export async function POST(request: Request) {
  const { name } = await request.json();
  return Response.json({name });
}
```

Thus it eliminates the need for a separate backend server in simple scenarios.

## Nested Layouts
`Nested layouts` is one of the distinguishing features of App Router. It allows you to create a layout that is shared across multiple pages. This is useful for creating a consistent look and feel across your application. To create a nested layout, you can create a `layout.tsx` file in the `app/` directory. The layout file will be automatically applied to all nested children. This is impossible in the Pages Router unless you manually wrap each child page with the Layout component, making your codebase clunky with this wrapping boilerplate.

## Loading.js

App Router automated the Suspense/Streaming feature of Nextjs. Let me explain.

In Pages Router, Nextjs created faster loading cf. Reactjs using Reactjs. React relied on pure async/await blocks that awaited the entire page load in case the fetched api block is slow to fetch. Consider this:

```js
export default async function Page() {
  // All components must finish fetching data before any HTML is sent to the client
  return (
    <div>
      <h1>Dashboard</h1>
      <UserProfile />
      <AnalyticsChart />
      <NotificationsFeed />
    </div>
  );
}
```
If say `AnalyticsChart` is slow to fetch data, the whole page waits.The browser sees a blank screen until everything is ready.

Enter Suspense/Streaming. This breaks the page into several components each loading parallely rather than serially. If you suspect a component's too heavy too load fast, you can wrap it in `<Suspense>` tags. For example:

```js
export default function Page() {
  return (
    <div>
      <h1>Dashboard</h1>
      {/* UserProfile is fast, so we render it directly */}
      <UserProfile />

      {/* AnalyticsChart might be slow, so we wrap it in Suspense */}
      <Suspense fallback={<LoadingChart />}>
        <AnalyticsChart />
      </Suspense>

      {/* NotificationsFeed might also be slow */}
      <Suspense fallback={<LoadingFeed />}>
        <NotificationsFeed />
      </Suspense>
    </div>
  );
}
```

The entire page appears to `stream`. That's why it's called Streaming.

Now enters App Router. Here when you add a `loading.js` file to a route segment in the `app/` directory, Next.js automatically wraps that segment in a Suspense boundary and uses your loading.js as the fallback UI. Cool, isn't it. Not the traditional SSR !

## Selective hydration

A webpage may contain client components as well as server components. The main component of the page itself is usually a server component. Now just like Suspense/Streaming prioritises  HTML of which UI components will be streamed from the server to client, Selective Hydration prioritises which client components will be hydrated on the client.

Traditionally the whole page waits for all JavaScript to load and then hydrates everything at once. But now React/Nextjs automatically detects the component with which the user is interacting (eg. button click) in real time, and hydrates that first.

Also, while `Suspense` is usually used for Server components which fetch data, Selective hydration only applies to client components (those with interactivity).

(Note that with Server Components (RSCs), the quantum of JS needed to be executed in the client was already reduced)

# Vercel's AI SDK

`npm install ai`

Then to install say the Vercel's OpenAI SDK: `npm install @ai-sdk/openai`

In Next.js 13+, files are Server Components by default. If you need interactivity (hooks, event handlers), you must mark the file as `'use client'`

THe most standard frontend UI code is:

```tsx
//frontend/app/page.tsx

'use client';
import { useState } from 'react';

export default function GeneratePage() {
  const [input, setInput] = useState('');
  const [response, setResponse] = useState('');

  // Explicit fetch-based submission
  const handleSubmit = async () => {
    const res = await fetch('/api/generate', {
      method: 'POST',
      body: JSON.stringify({ prompt: input }),
    });
    const { result } = await res.json();
    setResponse(result);
  };

  return (
    <div>
      <input
        value={input}
        onChange={(e) => setInput(e.target.value)}
      />
      <button onClick={handleSubmit}>Submit</button>
      <div>{response}</div>
    </div>
  );
}
```

But you can use Vercel's AI SDK to substitute this simple fetch call as follows:

1. **Vercel's useChat hook (Recommended for Chats)**
```tsx
'use client';
import { useChat } from 'ai/react'; // Vercel's pre-built hook

export default function ChatPage() {
  // useChat handles submission AND streaming automatically. No need to explicitly define handleSubmit function
  const { messages, input, handleInputChange, handleSubmit } = useChat({
    api: '/api/chat' // Points to your streaming API route
  });

  return (
    <div>
      {messages.map(m => (
        <div key={m.id}>{m.role}: {m.content}</div>
      ))}

      <form onSubmit={handleSubmit}>
        <input
          value={input}
          onChange={handleInputChange}
        />
        <button type="submit">Send</button>
      </form>
    </div>
  );
}
```

Note that you must use Vercel's `streamText` function in the API Route when you use `useChat` in the frontend.

2. **Vercel's useActions hook (Advanced Interactive UIs)**

The `useActions` hook is more flexible than `useChat` and is ideal when you need multiple AI actions or complex UI interactions. Here's how to use it:

```tsx
'use client';
import { useActions } from 'ai/react';

export default function AIActionsPage() {
  const { complete, isLoading } = useActions({
    api: '/api/ai/actions'
  });

  // You can define multiple AI actions
  const handleTranslate = async () => {
    const response = await complete('translate', {
      text: 'Hello world',
      targetLanguage: 'Spanish'
    });
    console.log(response.text); // "Hola mundo"
  };

  const handleSummarize = async () => {
    const response = await complete('summarize', {
      text: 'Long article content...'
    });
    console.log(response.summary);
  };

  return (
    <div>
      <button
        onClick={handleTranslate}
        disabled={isLoading}
      >
        Translate Text
      </button>
      <button
        onClick={handleSummarize}
        disabled={isLoading}
      >
        Summarize Text
      </button>
    </div>
  );
}
