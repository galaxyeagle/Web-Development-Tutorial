<style>
  h1 { font-size: 3em; }
  h2 { font-size: 2.5em; }
  h3 { font-size: 2em; }
  h4 { font-size: 1.5em; }
  h5 { font-size: 1.3em; }
  h6 { font-size: 1.2em; }
</style>


# Introduction

A database essentially supports 4 operations of CRUD, as discussed in [Chapter 5-Firebase](5-Firebase.md). If you further think logically, CRUD can be reduced to just 2 operations - read and write, with certain caveats.

In common parlance, a database is referred to as `server` and the Nodejs code (or some specialized software like DBeaver or Sqlite DB Browser) to access the database, is called `client`. A client sends CRUD requests (called `queries`) to the database server. 

# Local JSON data files

A JSON file acts like a simple, flat database. In other words, your `.json` file is the entire server. You read (using `fs.readFileSync`) and write (using `fs.writeFileSync`) the whole file for every operation.

When you read a JSON file in Node.js with `fs.readFileSync` and then use `JSON.parse()` on it, you get a **JavaScript array** from the JSON array. And then, all JavaScript arrays have these built-in methods:

- **`.push(item)`** - Adds an item to the end of the array.
- **`.find(callback)`** - Returns the first item that matches the condition in the callback.
- **`.findIndex(callback)`** - Returns the index of the first item that matches the condition.
- **`.filter(callback)`** - Returns a new array with only the items that match the condition.

---

 **EXAMPLE :**

Suppose your `users.json` contains:
```json
[
  { "id": 1, "name": "Alex" },
  { "id": 2, "name": "Sam" }
]
```

When you read and parse it in Node.js:
```js
const fs = require('fs');
const users = JSON.parse(fs.readFileSync('users.json', 'utf8'));
```
Now, `users` is a **JavaScript array**.  
You can use all array methods on it :

```js
// Add a user
users.push({ id: 3, name: "Jamie" });

// Find a user by id
const user = users.find(u => u.id === 2);

// Find the index of a user
const idx = users.findIndex(u => u.name === "Alex");

// Filter out a user (delete)
const remaining = users.filter(u => u.id !== 2);
```

---



# SQL Databases

SQL is a language/syntax to write database `queries`, much like Fetch is a syntax to write API calls. 

The SQL syntax for the 4 basic CRUD ops (assuming database is called `users`) are:

1. Create : `INSERT INTO users ...`
2. Read : `SELECT ... FROM users ...`
3. Update : `UPDATE users SET ...`
4. Delete : `DELETE FROM users ...`



## SQLite

Your entire database (all tables, indexes, and data) lives inside a single .db file (for example, mydatabase.db), which sits in your project directory. Like the .json file, here the .db file is your entire database server.

You need to design the client code to create the database and do CRUD on it in the following steps:

1. Create a client code file say `index.js` in your nodejs project folder.

2. `npm install better-sqlite3` : this library will help make the blueprint/`instance` of your database 

3. In your `index.js`, **create** an `instance` and use it to create a database `db`: 

```js
const Database = require('better-sqlite3');

// Create or open a database
const db = new Database('office.db');
```
4. Create a `coders` table
```js
db.exec(`
  CREATE TABLE IF NOT EXISTS coders (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT,
    designation TEXT
  )
`);
```

5. Do CRUD operations on the `users` table using a `db.prepare` template.

- **Create :** 
```js
const temp = db.prepare("INSERT into coders (name, designation) VALUES (? ?)")
temp.run("Sam","CEO");
temp.run("Ilya", "Tech lead");
```

- **Read :**
```js
const temp = db.prepare("SELECT (name, designation) FROM coders WHERE name LIKE ?")
read_coders = temp.all('S%'); // Reads all coders whose name starts with 'S'
console.log('selected coders are:`, coders.rows);
```

- **Update :**

```js
const temp = db.prepare("UPDATE coders SET designation = ? WHERE name = ?")
temp.run("CEO","Ilya");
temp.run("Sam", "Junior dev");
```

- **Delete :** 

```js
const temp = db.prepare("DELETE FROM coders WHERE name = ?");
temp.run("Sam");
```


## Server-based SQL databases

Unlike SQLIte, these databases live inside a server process running in the background, not as a single file in your project. In tech parlance, they are said to have a `client/server` architecture. Examples include `PostgreSQL`, `MySQL`, `SQL Server`, etc.

### PostgreSQL

#### Using `pg` (node-postgres) client library :

`npm install pg` : Node-postgres (`pg`) is a Nodejs libarary to create a client instance to connect to a postgres database

**SET UP A CONNECTION :**

Assuming your Nodejs file is the client and database is the server, 
If your host is set to "`localhost`" or "`127.0.0.1`", it connects to a PostgreSQL server running on your own computer, usually listening on port `5432`. For this connection we do:


```js
const { Client } = require('pg');
const client = new Client({
  user: 'myuser',
  host: 'localhost',
  database: 'mydb',
  password: 'mypassword',
  port: 5432,
});
await client.connect();
```
The json object argument within `Client()` is called the `connection string`.

**CRUD :**

Then the CRUD ops use the `query()` function on the instantiated client instance:
```js
await client.query('INSERT INTO users (name) VALUES ($1)', ['Alex']);
const res = await client.query('SELECT * FROM users WHERE name = $1', ['Alex']);
await client.query('UPDATE users SET name = $1 WHERE name = $2', ['Sam', 'Alex']);
await client.query('DELETE FROM users WHERE name = $1', ['Sam']);

await client.end();
```

Note that we put a placeholder say `$1` and passed parameters as `arrays` to prevent SQL injection malwares.

#### Using `postgres.js` client library

Compared with the classic `pg`, `postgres,js` is a more modern PostgreSQL client

```js
import postgres from 'postgres';
```

**SET UP A CONNECTION :**

You create a `client` instance by instantiating it with the `connection string` of your database, and use it directly for CRUD :
```js
const client = postgres(process.env.POSTGRES_URL);
```
Here your `connection string`, say `postgres://postgres:yourpassword@localhost:5432/mydb` is stored as an env variable named `POSTGRES_URL`. You can get this from your database provider's dashboard/settings.

**CRUD :**

```js
await client`SELECT * FROM users`;
```

#### Using `postgres.js` + `Drizzle ORM`

Till now we were writing raw SQL like `db.run` or `client.query` or `client'..raw sql query..'` to do CRUD. You can abstract that using an ORM like `Drizzle`, `Prisma`, `Sequelize`, `TypeORM`, etc. 

This allows you to treat the entire database as a large json object add simply adding and deleting key-value pairs will cause CRUD ! No raw sql ! Here's the syntax:

After `client` instance is instantiated, create a drizzle instance `db` from it and do CRUD on that :

```js
const db = drizzle(client);

// CREATE
await db.insert(user).values({ email, password: hashedPassword });

// READ
await db.select().from(user).where(eq(user.email, email));

// UPDATE
await db.update(user).set({ password: newPassword }).where(eq(user.id, id));

// DELETE
await db.delete(user).where(eq(user.id, id));
```

Wow ! This is like json add/delete syntax abstracting away the boring SQL syntax.

**Here's how it works:**

- You use a TypeScript ORM (Drizzle), which provides a type-safe, object-oriented way to build queries.

- Instead of writing SQL strings, you use function calls (db.insert(...).values(...), etc.).

- You define your schema in code, so you get autocomplete, type safety, and fewer runtime errors.




#### Cloud Postgres Databases

e.g. `Neon`, `AWS RDS`, `Heroku Postgres`, `Supabase`. They host and manage your PostgreSQL (or other) DB in the cloud, giving you a serverless experience. You connect to these DBs from anywhere using a `connection string`.

Let's discuss Neon.

---

**Step 1: Create a Neon Database**

- Go to [Neon’s dashboard](https://neon.tech/), sign up/log in, and create a new project.
- Neon auto-creates a database (e.g., `neondb`) for you.

---

**Step 2: Get Your Connection String**

- In your Neon project dashboard, navigate to **Connection Details**.
- Copy the **Postgres connection string** (it looks like:  
  `postgres://username:password@host:port/dbname?sslmode=require`)
- Add it to your `.env` file:
  ```
  DATABASE_URL=postgres://username:password@host:port/dbname?sslmode=require
  ```
  *(You may also see `NEON_DATABASE_URL` in some guides; both work as long as you use the same variable in your code.)*

---

**Step 3: Install Dependencies**

```sh
npm install drizzle-orm @neondatabase/serverless dotenv
npm install -D drizzle-kit
```
- `drizzle-orm`: The ORM itself.
- `@neondatabase/serverless`: Neon’s HTTP driver for serverless environments.
- `dotenv`: Loads your `.env` file.
- `drizzle-kit`: For migrations and schema generation.

---

**Step 4: Generate Drizzle Object from Neon's instance**

**`src/db/db.ts`:**
```ts
import { drizzle } from 'drizzle-orm/neon-http';
import { neon } from '@neondatabase/serverless';
import * as schema from './schema'; // Import your schema definitions
import 'dotenv/config';

const sql = neon(process.env.DATABASE_URL!);
export const db = drizzle(sql, { schema });
```
- This sets up a Drizzle instance connected to your Neon database using the HTTP driver.
- NB. We added a {schema} object to the argument just to make the generated drizzle instance type-safe by pre-telling the compiler the structure of our database.
- Neon’s cloud database is designed for HTTP/WebSocket access which modern edge CDN++s like Vercel use for accessing cloud databases. But using postgres.js or pg with Neon’s HTTP endpoint won’t work, because those libraries expect a TCP connection. Hence we use neon's own library.

---

**Step 5: Define Your Database Schema**

**`src/db/schema.ts`:**
```ts
import { pgTable, serial, varchar } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: serial('id').primaryKey(),
  name: varchar('name', { length: 100 }),
  email: varchar('email', { length: 255 }),
});
```
- This defines a `users` table with `id`, `name`, and `email` columns.

---

**Step 6: Configure Drizzle Kit for Migrations**

**`drizzle.config.ts`:**
```ts
import type { Config } from 'drizzle-kit';

export default {
  schema: './src/db/schema.ts',
  out: './drizzle/migrations',
  driver: 'pg',
  dbCredentials: {
    connectionString: process.env.DATABASE_URL!,
  },
} satisfies Config;
```
- This tells Drizzle Kit where your schema and migrations live, and how to connect to Neon.

---

**Step 7: Generate and Run Migrations**

As your app evolves, you’ll need to change the `schema` (add columns, rename tables, etc.). `Migrations` help capture these changes like a version-control. They are like "changelog" for your database. Whenever you change your schema file, run these commands in the terminal:

- **Generate migration files:**
  ```sh
  # Drizzle compares your code schema with the current database and generates a migration file

  npx drizzle-kit generate:pg
  ```
- **Apply migrations to Neon:**
  ```sh
  # This updates the actual database schema to match your code
  npx drizzle-kit migrate:pg
  ```
- This keeps your `Neon database` schema in sync with your `schema.ts` code.

---

**Step 8: CRUD Operations with Drizzle ORM**

**`src/db/queries.ts`:**
```ts
import { db } from './db';
import { users } from './schema';

// CREATE
await db.insert(users).values({ name: 'Alice', email: 'alice@example.com' });

// READ
const allUsers = await db.select().from(users);
const user = await db.select().from(users).where(users.email.eq('alice@example.com'));

// UPDATE
await db.update(users)
  .set({ name: 'Alice Cooper' })
  .where(users.email.eq('alice@example.com'));

// DELETE
await db.delete(users).where(users.email.eq('alice@example.com'));
```
- All queries are type-safe and work directly with your Neon cloud database.

---

**Step 9: Run Your Code**

If you’re using TypeScript, run with [tsx](https://www.npmjs.com/package/tsx) or [bun](https://bun.sh/):

```sh
npx tsx src/db/queries.ts
# or
bun src/db/queries.ts
```
- Make sure your `.env` file is loaded and your Neon database is accessible.
- Thus `queries.ts` is the actual `client`, while other codes like db.ts, config,ts, schema.ts, etc. are just supporting modules.
---

**Summary Table**

| Step                    | File/Command                | Purpose                                        |
|-------------------------|-----------------------------|------------------------------------------------|
| Create Neon DB          | Neon dashboard              | Cloud Postgres instance                        |
| Get connection string   | `.env`                      | Securely store Neon credentials                |
| Install dependencies    | `npm install ...`           | Set up Drizzle, Neon driver, etc.              |
| Configure Drizzle       | `src/db/db.ts`              | Connect Drizzle to Neon                        |
| Define schema           | `src/db/schema.ts`          | Model your tables                              |
| Drizzle config          | `drizzle.config.ts`         | Migration and schema management                |
| Generate/migrate schema | CLI commands                | Sync schema to Neon                            |
| CRUD operations         | `src/db/queries.ts`         | Interact with your Neon DB via Drizzle         |

---

**You now have a modern, type-safe, cloud-native PostgreSQL stack using Neon and Drizzle ORM!**  
You can scale this pattern for real-world apps, Next.js APIs, serverless functions, and more


#### Managed Database Services

e.g. `Vercel`, `Supabase`, `Railway`, etc.




















---

- **Environment variables** (like `process.env.DATABASE_URL`) are **only available on the server** in Next.js.
- **Client components** (anything with `"use client"`) run in the browser, which **cannot read server environment variables**.
- If you import your database client (`db` from `@/lib/db`) into a client component, it tries to access `process.env.DATABASE_URL` in the browser—**which fails**.

**Analogy:**  
Imagine your `.env` file is a secret recipe book in your kitchen (server). If you try to use it in your living room (browser/client), you can’t—because the book never leaves the kitchen!




- Some Postgres drivers (like `pg`) try to import Node.js modules (`fs`) which **don’t exist in the browser or edge/serverless runtimes**.
- If you import your DB code into a client component, Next.js tries to bundle it for the browser—and fails when it sees `require('fs')`.

**Analogy:**  
It’s like trying to use a kitchen blender in your car. The car (browser) doesn’t have the right power outlet (Node.js APIs), so it just won’t work.


3. **How to Fix: The Feynman Way**

**A. Never Import DB Code in Client Components**

- **Server components** (no `"use client"`) can access the DB.
- **Client components** (with `"use client"`) **cannot** access the DB or `process.env`.

**B. Two Patterns to Use**

**Pattern 1: Server Component Fetches Data**

1. **Fetch data in a server component** (e.g., `page.tsx`).
2. **Pass data as props** to a client component for interactivity.

**Example:**
```tsx
// app/profile/page.tsx (Server Component)
import db from "@/lib/db";
import { ProfileForm } from "@/components/ProfileForm";

export default async function ProfilePage() {
  const user = await db.select().from(users).where(...);
  return ; // ProfileForm can be a client component
}
```

**Pattern 2: API Route + Client Fetch**

1. **Create an API route** (e.g., `/api/user`) that fetches from the DB (server-side).
2. **Fetch from this API route** in your client component using `fetch`, `useSWR`, or React Query.

**Example:**
```js
// app/api/user/route.ts (Server Only)
import db from "@/lib/db";
export async function GET() {
  const user = await db.select().from(users).where(...);
  return Response.json(user);
}
```
```js
// app/profile/page.tsx (Client Component)
"use client";
import useSWR from "swr";
export default function ProfilePage() {
  const { data: user } = useSWR("/api/user", fetcher);
  // render UI
}
```


4. **How to Refactor Your DB Code for Neon + Drizzle + Next.js**

- **Install Neon’s serverless driver** (if using Neon):
  ```bash
  npm install @neondatabase/serverless
  ```
- **lib/db.ts** (for Drizzle + Neon):
  ```ts
  import { drizzle } from 'drizzle-orm/neon-http';
  import { neon } from '@neondatabase/serverless';

  const sql = neon(process.env.DATABASE_URL!); // Only runs server-side!
  const db = drizzle(sql);

  export default db;
  ```
- **Never import this file in client components!**

---
























## My Database Stack (PostgreSQL + Drizzle ORM + Neon remote + Nextjs/App Router)

In your local project folder, create a `lib` folder having the following files:

```
/lib
  ├── db.ts         # Connects Drizzle to Neon via neon-http
  ├── schema.ts     # Contains schema definition using pg-core
  ├── seed.ts       # Seeds DB using db instance
  └── verify-seed.ts # Optional: verify seeded data
```

**You'll need to import and use certain libraries as follows:**


| Purpose                        | Used Package               | Required? | Typical File     Snippet                                                                                                                                      |
| ------------------------------ | -------------------------- | :-------: | ---------------- | 
| **Define schema**              | `drizzle-orm/pg-core`      | ✅ Yes     | `/lib/schema.ts` | 
| **Run queries with Neon**      | `@neondatabase/serverless` | ✅ Yes     | `/lib/db.ts`     | 
| **Connect Drizzle to Neon**    | `drizzle-orm/neon-http`    | ✅ Yes     | `/lib/db.ts`     | 
| **Old driver (Node.js-based)** | `pg`                       | ❌ No      | ❌ (Deprecated)   | 

**The core illustrative code in the files would appear as follows:**

```ts
//db.ts

import { drizzle } from 'drizzle-orm/neon-http';
import { neon } from '@neondatabase/serverless';

if (!process.env.DATABASE_URL) {
  throw new Error('DATABASE_URL environment variable is not set');
}
const sql = neon(process.env.DATABASE_URL);

const db = drizzle(sql);

export default db;
```
Then

```ts
//schema.ts

import { pgTable, serial, varchar, integer, real, boolean, text } from 'drizzle-orm/pg-core';

export const usersTable = pgTable('users', {
  id: serial('id').primaryKey(),
  name: varchar('name', { length: 100 }),
  email: varchar('email', { length: 100 }),
});
```
After defining/modifying the schema, to push it to Neon Remote run:
```sh
# Bash

npx drizzle-kit generate # creates a new migration sql file based on your schema.ts

npx drizzle-kit push  # applies pending migration to your neon remote
```
You can also use the `migrate` command instead of `push` above.

Then,

```ts
//seed.ts

import { users } from "./schema";
import db from "./db";

const dummyUsers = [
  {
    name: "Aarav Sharma",
    email: "aarav.sharma@example.com",
  },
  {.....}
]

async function seed() {
  for (const user of dummyUsers) {
    await db.insert(users).values(user);
  }
  console.log("Seeded dummy users.");
}

seed().catch(console.error);
```

After updating the data in seed.ts, to push that to Neon remote run:
`npx tsx lib/seed.ts` in terminal.

---

**Observation 1:**

The Neon serverless driver allows you to query Postgres databases over HTTP or WebSockets instead of the traditional TCP protocol used by standard Postgres drivers like pg.You install it as a package (e.g., @neondatabase/serverless) and use it in your code to interact with Neon-hosted Postgres databases.