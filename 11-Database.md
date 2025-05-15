<style>
  h1 { font-size: 3em; }
  h2 { font-size: 2.5em; }
  h3 { font-size: 2em; }
  h4 { font-size: 1.5em; }
  h5 { font-size: 1.3em; }
  h6 { font-size: 1.2em; }
</style>


# Introduction

A database essentially supports 4 operations CRUD, as discussed in Chapter 5-Firebase. If you think logically, CRUD can be reduced to just 2 operations - read and write, with certain caveats.

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

Assuming your SQL client is a Nodejs file, 

1. `npm install pg`
2.


