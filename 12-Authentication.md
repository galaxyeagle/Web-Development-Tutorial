# Authentication in Web Development

Authentication is the process of verifying the identity of a user or system. It is a fundamental part of almost every web application, ensuring that only authorized users can access certain resources or perform specific actions.

**WHY AUTHENTICATION?**

- **Security:** Prevent unauthorized access to sensitive data or actions.
- **Personalization:** Show user-specific content (e.g., dashboards, profiles).
- **Tracking:** Keep track of user activity, preferences, and history.

# Cookies

Cookie-based auth persists between sessions unlike `local storage`. The cookie lifecycle follows a similar sequence in different server environments. You can refer [this](https://www.aleksandrhovhannisyan.com/blog/http-cookies/) article to get an idea about cookies.

## In a Nodejs Server 

The basic authentication works through cookies in the following sequence :

1. **Request**: The client sends a GET/POST Request to an Endpoint in the Nodejs server.
2. **Setting Cookies: Route Def & Response:** The server's response to the client has a `Set-Cookie` header with all the cookie details like name, expiry date, security, etc.
3. **Future Requests by Client** : The browser receives this response and stores the cookie and henceforth all future Requests by the Client include those cookie details in a `cookie` header, so long as that cookie doesn't expire.
4. **Getting Cookies: Server reads Cookie** : Either using 
  - `req.cookies` (with `cookie-parser` middleware) - Parses the Cookie header and exposes each cookie as a property on req.cookies.
  
    or using 

  - `req.session` (with `express-session` middleware) - On each request, Express reads the session ID from the cookie, looks up the session data on the server, and attaches it to req.session

## In a Nextjs app

The sequence here is as follows:

**1. Request:** Client Sends a GET/POST Request to a route defined in the `app/` directory (e.g., `app/api/cookies/route.ts`). These routes handle server-side logic for cookie operations.

Example: A client sends a POST request to `/api/cookies` to set a cookie or a GET request to retrieve data based on cookies.

**2. Setting Cookies:** Route Definition & Response
In the route handler, you can set cookies in the response using the `cookies` API provided by Next.js. The `cookies().set()` method is used to create a `Set-Cookie` header with cookie details like name, value, expiry, and security options.

- **Example: Setting a Cookie**

Create a route handler at `app/api/cookies/route.ts`:

```typescript
import { cookies } from 'next/headers';
import { NextResponse } from 'next/server';

export async function POST(request: Request) {
  const { value } = await request.json();

  // Set a cookie
  cookies().set({
    name: 'user-session',
    value: value,
    httpOnly: true, // Prevents client-side JavaScript access
    secure: process.env.NODE_ENV === 'production', // Only send over HTTPS in production
    maxAge: 60 * 60 * 24, // Expires in 24 hours
    path: '/', // Cookie available site-wide
  });

  return NextResponse.json({ message: 'Cookie set successfully' });
}
```

**Key Cookie Options:**
- `httpOnly`: Prevents client-side JavaScript from accessing the cookie, enhancing security.
- `secure`: Ensures the cookie is only sent over HTTPS (set conditionally for production).
- `maxAge`: Sets the cookie's expiration time in seconds.
- `path`: Specifies the scope of the cookie (e.g., `'/'` for site-wide).

Notice that instead of `Response.json()` as described [here](9-NextJS.md/#route-handlers-as-frontend-substitutes-of-rest-apis), we use `NextResponse.json()`, otherwise the cookie set with cookies().set would not be included in the response headers. 

**3. Future Requests by Client**
Once the server sets the cookie, the client's browser stores it and includes it in the `Cookie` header of all subsequent requests to the same domain, as long as the cookie hasn't expired. The browser automatically handles this based on the cookie's attributes (e.g., `path`, `maxAge`).

**4. Getting Cookies: Server Reads Cookies**
In Next.js App Router, you can read cookies on the server using the `cookies().get()` function from `next/headers`. This function provides access to the cookies sent in the request's `Cookie` header.

- **Example: Reading a Cookie**

Create a route handler to read the cookie at `app/api/get-cookies/route.ts`:

```typescript
import { cookies } from 'next/headers';
import { NextResponse } from 'next/server';

export async function GET() {
  const cookieStore = cookies();
  const userSession = cookieStore.get('user-session');

  if (userSession) {
    return NextResponse.json({ value: userSession.value });
  } else {
    return NextResponse.json({ message: 'No cookie found' }, { status: 404 });
  }
}
```



- **Session Management**

For session management, you can use cookies to store a session ID and manage session data server-side. Libraries like `next-auth` or custom solutions with JSON Web Tokens (JWT) are commonly used for this purpose.

- **Example with JWT:**

Note that earlier with cookies().set(), we assigned cookie value from request body. If you want further customisation and security, you can create a JWT token from the request body data and store that as the value of the cookie instead. This is illustrated below.

```typescript
import { cookies } from 'next/headers';
import { NextResponse } from 'next/server';
import jwt from 'jsonwebtoken';

export async function POST(request: Request) {
  const { userId } = await request.json();

  // Create a JWT
  const token = jwt.sign({ userId }, process.env.JWT_SECRET!, { expiresIn: '1d' });

  // Set the JWT as a cookie
  cookies().set({
    name: 'auth-token',
    value: token,
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    maxAge: 60 * 60 * 24,
    path: '/',
  });

  return NextResponse.json({ message: 'Session created' });
}
```

- **Reading the JWT:**

```typescript
import { cookies } from 'next/headers';
import { NextResponse } from 'next/server';
import jwt from 'jsonwebtoken';

export async function GET() {
  const token = cookies().get('auth-token')?.value;

  if (!token) {
    return NextResponse.json({ message: 'No session' }, { status: 401 });
  }

  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET!);
    return NextResponse.json({ user: decoded });
  } catch (error) {
    return NextResponse.json({ message: 'Invalid session' }, { status: 401 });
  }
}
```

- **Best Practices**

1. **Use `httpOnly` Cookies**: Prevent client-side access to sensitive cookies (e.g., session tokens).
2. **Secure Cookies in Production**: Always set `secure: true` in production to ensure cookies are sent over HTTPS.
3. **Set Appropriate Expiry**: Use `maxAge` or `expires` to control cookie lifetime.
4. **Use `sameSite`**: Set `sameSite: 'strict'` or `sameSite: 'lax'` to mitigate CSRF attacks.
5. **Validate and Sanitize Inputs**: When setting cookies based on user input, validate and sanitize to prevent injection attacks.
6. **Use Libraries for Sessions**: For complex session management, consider `next-auth` or similar libraries to handle authentication and sessions securely.

- **Notes**
- The `cookies()` API in Next.js is part of the `next/headers` module and is designed for server-side use in App Router.
- Unlike Express's `cookie-parser` or `express-session`, Next.js provides a built-in `cookies()` API, reducing the need for middleware.
- For client-side cookie access (if needed), use libraries like `js-cookie`, but avoid storing sensitive data in non-`httpOnly` cookies.
- Ensure environment variables (e.g., `JWT_SECRET`) are set in `.env` files and accessed securely.

This approach leverages Next.js's native capabilities for cookie management while following security best practices.


**Setting cookies:** Use `cookies().set()` in Route Handlers, and response.cookies.set() in Middleware.

**Middleware:** 

Use `request.cookies.get('name')` to read cookies.

**Route Handlers:** Use `cookies().get('name')` to read cookies.


## Creating cookies in client-side using JS

You need to use `document.cookies()` function to create cookies client-side (eg. from an interactive client component/user input or from the browser dev tools) but its risky and if your cookie-get function is configured to read secure cookies only, this method won't work.

---

# Types of Authentication

### a. **Session-based Authentication**
- Traditional approach.
- Server creates a session for the user after login and stores a session ID in a cookie.
- On each request, the server checks the session ID.




### b. **Token-based Authentication (JWT)**
- Modern approach, especially for SPAs and APIs.
- After login, server issues a signed token (e.g., JWT) to the client.
- Client sends the token in the `Authorization` header for each request.
- Server verifies the token signature and grants access.

### c. **Third-party Authentication (OAuth, Google, GitHub, etc.)**
- Users log in using external providers.
- Common for social logins ("Sign in with Google").

---

# Authentication Flow in a Fullstack App

1. **User submits login form** (username/email + password) from the frontend.
2. **Frontend sends credentials** to backend via a POST request (e.g., `/api/login`).
3. **Backend verifies credentials** (checks database).
4. If valid, **backend responds** with a session cookie or JWT token.
5. **Frontend stores** the token (in memory, localStorage, or as a cookie).
6. **Subsequent requests** include the token for protected routes.

---

# My Auth stack (NextAuth with Google OAuth

Your app has many routes. Each renders various components (server and client components). Your aim is that any of those components will only be rendered when auth session is active, otherwise redirect the user to the sign in page. 

First follow Steps 1 & 2 from [this tutorial](https://codreline.hashnode.dev/integrate-google-authentication-in-your-next-project).

Note that in my stack I use the Next Auth v5 version (beta) which should be installed like this:

`npm i next-auth@beta`

Also keep handy the [Next v5 tips](https://authjs.dev/getting-started/migrating-to-v5) throughout.

Then follow my below guide which is partly inspired by [Jack Herrington's setup](https://github.com/jherr/next-auth-v5/tree/main).

Let's start with the homepage (`/` route). 

```ts
//app/page.tsx

import { auth } from "@/auth";
import { redirect } from "next/navigation";
import AuthButton from "./AuthButton.client";

export default async function Home() {
  const session = await auth();
  
  if (session?.user) {
    redirect("/dashboard");
  }

  return (
    <pre>{JSON.stringify(session, null, 2)}</pre>
    <AuthButton />
  );
}
```

We get the `session` json object from the `auth()` function.

Infact apart from this root page, for every other page (RSC) you can add the following at the start of the default component to make it a protected route:

```
const session = await auth();
  if (!session) {
    redirect("/");
  }
```

 Now let's see where we get this `auth()` from. 


```ts
//auth/index.ts
// This is the main auth config file


import NextAuth from "next-auth";
import Google from "next-auth/providers/google";


export const authOptions = {
  providers: [
    Google({
      clientId: process.env.GOOGLE_CLIENT_ID!,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET!,
      authorization: {
        params: {
          prompt: "consent",
          access_type: "offline",
          response_type: "code"
        }
      }
    }),
  ],
  secret: process.env.AUTH_SECRET,
  session: {
    strategy: "jwt",
  },
  pages: {
    signIn: "/",  
  },
  trustHost: true,
  callbacks: {
    async jwt({ token, account, profile }) {
      if (account) {
        token.email = profile?.email;
      }
      return token;
    },
    async session({ session, token }) {
      if (session.user) {
        session.user.id = token.sub ?? "";
        session.user.email = token.email ?? "";
      }
      return session;
    },
  },
} satisfies Parameters<typeof NextAuth>[0];

export const { handlers, auth, signIn, signOut } = NextAuth(authOptions);

```
Some observations from the above auth config:

1. The last line `export const { handlers, auth, signIn, signOut } = NextAuth(authOptions);` is the most important line. It exports 4 vital functions, one of which is `auth()` that we used for getting current session.

2. Only Google is added as the OAuth provider for illustration.

3. `jwt` is used as session strategy. Thus the session would be a cookie wrapped in a jwt token as we discusses earlier. If you instead use the `database` strategy, the session details would be stored in a separate database which I think is complicated and an overkill.

Also notice that in the `app/page.tsx`, there's an `<AuthButton>`. Its purpose is to show “Sign In” if user not logged in, “Sign Out” if logged in. This component is realised through a client and server component pair, as follows:

```ts
//app/AuthButton.client.tsx

"use client";
import { useSession } from "next-auth/react";

import { Button } from "@/components/ui/button";

import { signIn, signOut } from "@/auth/helper";

export default function AuthButton() {
  const session = useSession();

  return session?.data?.user ? (
    <Button
      onClick={async () => {
        await signOut();
        await signIn();
      }}
    >
      {session.data?.user?.name} : Sign Out
    </Button>
  ) : (
    <Button onClick={async () => await signIn()}>Sign In</Button>
  );
}
```

and 

```ts
//app/AuthButon.server.tsx

import { SessionProvider } from "next-auth/react";
import { auth } from "@/auth";

import AuthButtonClient from "./AuthButton.client";

export default async function AuthButton() {
  const session = await auth();
  if (session && session.user) {
    session.user = {
      name: session.user.name,
      email: session.user.email,
    };
  }

  return (
    <SessionProvider session={session}>
      <AuthButtonClient />
    </SessionProvider>
  );
}
```

Observe the following:

1. The interactivity is handled by the client component which is imported in the server component. This is as per the `islands architecture`.

2. On the server, `auth()` fetches the session. On the client, `useSession()` provides session state. `<SessionProvider>` ensures both are in sync.

3. The `signIn()` and `signOut()` functions are imported from `/auth/helper.ts`. So let's check that file:

```ts
//auth/helper.ts

"use server";
import { signIn as naSignIn, signOut as naSignOut } from ".";

export async function signIn() {
  await naSignIn();
}

export async function signOut() {
  await naSignOut();
} 
```


- **THE REAL MAGIC OF NEXTAUTH:**

Notice that ultimately, the `signIn` and `signOut` functions in `/auth/helper.ts` above come from the main auth config file. When triggered they run the `app/api/auth/signin/google` and `/app/api/auth/signout` endpoints respectively. The OAuth flow looks as follows:

```
User Browser
    |
    | 1. Clicks "Sign In"
    v
Your App (/api/auth/signin/google)
    |
    | 2. Redirects to Google OAuth
    v
Google Consent Screen
    |
    | 3. User logs in, grants access
    v
Google
    |
    | 4. Redirects to callback URL with code
    v
Your App (/api/auth/callback/google)  <--- "callback" request
    |
    |Google OAuth
    |
    | (after user authenticates)
    v
Your App: /api/auth/callback/google  <-- "callback" request
    |
    | 5. NextAuth exchanges code for tokens & user profile
    |
    |--> Calls async jwt({ token, account, profile, ... })
    |      (Customize JWT token here)
    |
    |--> Calls async session({ session, token, ... })
    |      (Customize session object here)
    |
    v
Session is created and user is signed in & redirected to dashboard
```

The `callback` is a route your app exposes for the OAuth provider (Google) to send the user and their authorization code back to your app. The callback URL must be registered in your Google Cloud Console as an "Authorized redirect URI" to work. NextAuth handles all the heavy lifting at this endpoint: exchanging codes, fetching tokens, and setting up the session. For that  NextAuth triggers your `jwt` and `session` callbacks defined in `auth/index.ts` as part of creating the user session.

**Now the magic of NextAuth is that** you don't need to define `/api/auth/signin/google`, `/api/auth/callback/google` and `/api/auth/signout` endpoints separately. You just create a catch-all route handler as follows, and NextAuth creates all the endpoints under the hood.

```ts
//app/api/auth/[...nextauth]/route.ts

import { handlers } from "@/auth";
export const { GET, POST } = handlers;
```
Yes it's that simple !

There's one last concern. What if a user doesn't go to the `/` route to sign in, but goes directly to some other route, say `/bookmarks`? There must be a middleware to handle such protected routes which pre-processes every fetch request (unless it's for an api route or static asset) by which they're redirected back to the `/api/auth/signin` api route if they are not authenticated and not on home page. So here it is.

```ts
//middleware.ts

import { NextResponse } from "next/server";
import { auth } from "@/auth";

export const config = {
  matcher: ["/((?!api|_next/static|_next/image|favicon.ico).*)"],
};

export default auth((req) => {
  const reqUrl = new URL(req.url);
  if (!req.auth && reqUrl?.pathname !== "/") {
    return NextResponse.redirect(
      new URL(
        `/api/auth/signin?callbackUrl=${encodeURIComponent(
          reqUrl?.pathname
        )}`,
        req.url
      )
    );
  }
});
```
- **HOPE YOU UNDERSTAND HOW REDIRECTION IS HAPPENING :**

- *Redirecting Signed-In Users to /dashboard :*

When a user lands on the home page (/), the server checks if there’s a valid session (session?.user). If the user is signed in, they are immediately redirected to /dashboard as per the code in `/app/page.ts`.

- *Redirecting Signed-Out Users to / :*

This is handled in your `middleware.ts` file which will redirect to the /api/auth/signin endpoint. This will lead you to the Google OAuth Sign In page, After signing in, the middleware.ts code ensures the user is sent back to the page they originally tried to access (using `callbackUrl`). But usually its the  `/` home page which would redirect to `/dashboard` after sign-in.




# Example: Email/Password Authentication with Express & JWT

### a. **Backend (Express + JWT)**

```js
// Install: npm install express jsonwebtoken bcryptjs
const express = require('express');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');
const app = express();

app.use(express.json());

const users = []; // In-memory user store (replace with DB in production)
const SECRET = 'your_jwt_secret';

// Register endpoint
app.post('/api/register', async (req, res) => {
  const { email, password } = req.body;
  const hashed = await bcrypt.hash(password, 10);
  users.push({ email, password: hashed });
  res.status(201).send('User registered');
});

// Login endpoint
app.post('/api/login', async (req, res) => {
  const { email, password } = req.body;
  const user = users.find(u => u.email === email);
  if (!user || !(await bcrypt.compare(password, user.password))) {
    return res.status(401).send('Invalid credentials');
  }
  const token = jwt.sign({ email }, SECRET, { expiresIn: '1h' });
  res.json({ token });
});

// Protected route
app.get('/api/profile', (req, res) => {
  const auth = req.headers.authorization;
  if (!auth) return res.status(401).send('No token');
  try {
    const payload = jwt.verify(auth.split(' ')[1], SECRET);
    res.json({ email: payload.email });
  } catch {
    res.status(401).send('Invalid token');
  }
});

app.listen(3000, () => console.log('Server running on port 3000'));
```


### b. Frontend (React Example)

```js
import React, { useState } from 'react';

function Login() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');
  const [token, setToken] = useState('');

  const handleLogin = async () => {
    const res = await fetch('/api/login', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ email, password })
    });
    const data = await res.json();
    setToken(data.token);
  };

  return (
    <div>
      <input value={email} onChange={e => setEmail(e.target.value)} placeholder="Email"/>
      <input type="password" value={password} onChange={e => setPassword(e.target.value)} placeholder="Password"/>
      <button onClick={handleLogin}>Login</button>
      {token && <div>Logged in! Token: {token}</div>}
    </div>
  );
}
```

# 5. Using Firebase Authentication
Firebase provides a ready-to-use authentication service with support for email/password, Google, Facebook, etc.

**Steps:**

1. Initialize Firebase in your React app (see 5-Firebase.md).
2. Use Firebase Auth methods:

```js
import { getAuth, signInWithEmailAndPassword, createUserWithEmailAndPassword } from "firebase/auth";

const auth = getAuth();

// Register
createUserWithEmailAndPassword(auth, email, password);

// Login
signInWithEmailAndPassword(auth, email, password);
```


# 6. Protecting Routes (Frontend & Backend)

- **Frontend**: Use route guards (e.g., React Router's PrivateRoute) to restrict access to authenticated users.

- **Backend**: Always check for valid tokens or sessions before serving protected resources.


# 7. Best Practices
- Never store plain passwords; always hash them (e.g., with bcrypt).
- Use HTTPS to protect credentials in transit.
- Store tokens securely (prefer HTTP-only cookies for sensitive tokens).
- Implement logout by deleting the token/session.
- Use environment variables for secrets.

