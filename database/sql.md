To use SQL databases in an `Express.js` application, you usually follow this structure:

1. Install a SQL database
2. Install a Node.js SQL driver or ORM
3. Connect Express to the database
4. Run queries (CRUD operations)

The most common SQL databases are:

* MySQL
* PostgreSQL
* SQLite
* Microsoft SQL Server

For beginners with Express.js:

* MySQL → easier hosting and tutorials
* PostgreSQL → powerful and modern
* SQLite → simplest for local testing

---

# 1. Create Express Project

```bash
mkdir express-sql
cd express-sql

npm init -y
```

Install Express:

```bash
npm install express
```

---

# 2. Choose SQL Database

## Option A — MySQL

Install MySQL driver:

```bash
npm install mysql2
```

Official website:

[MySQL](https://www.mysql.com/?utm_source=chatgpt.com)

---

## Option B — PostgreSQL

Install PostgreSQL driver:

```bash
npm install pg
```

Official website:

[PostgreSQL](https://www.postgresql.org/?utm_source=chatgpt.com)

---

# 3. Project Structure

```txt
express-sql/
│
├── server.js
├── db.js
├── package.json
```

---

# 4. Connect Database

# MySQL Example

## db.js

```js
import mysql from "mysql2/promise";

export const db = await mysql.createConnection({
    host: "localhost",
    user: "root",
    password: "password",
    database: "testdb"
});

console.log("MySQL Connected");
```

---

## server.js

```js
import express from "express";
import { db } from "./db.js";

const app = express();

app.use(express.json());

app.get("/", async (req, res) => {

    const [rows] = await db.query(
        "SELECT * FROM users"
    );

    res.json(rows);
});

app.listen(3000, () => {
    console.log("Server running");
});
```

---

# 5. Enable ES Modules

In `package.json`:

```json
{
  "type": "module"
}
```

---

# 6. Create Database & Table

Inside MySQL:

```sql
CREATE DATABASE testdb;

USE testdb;

CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
```

Insert data:

```sql
INSERT INTO users(name,email)
VALUES('Shaddat','test@gmail.com');
```

---

# 7. Run Server

```bash
node server.js
```

Open:

```txt
http://localhost:3000
```

You will get:

```json
[
  {
    "id":1,
    "name":"Shaddat",
    "email":"test@gmail.com"
  }
]
```

---

# CRUD Operations

# Create

```js
app.post("/users", async (req, res) => {

    const { name, email } = req.body;

    await db.query(
        "INSERT INTO users(name,email) VALUES(?,?)",
        [name, email]
    );

    res.send("User Added");
});
```

---

# Read

```js
app.get("/users", async (req, res) => {

    const [rows] = await db.query(
        "SELECT * FROM users"
    );

    res.json(rows);
});
```

---

# Update

```js
app.put("/users/:id", async (req, res) => {

    const { name } = req.body;

    await db.query(
        "UPDATE users SET name=? WHERE id=?",
        [name, req.params.id]
    );

    res.send("Updated");
});
```

---

# Delete

```js
app.delete("/users/:id", async (req, res) => {

    await db.query(
        "DELETE FROM users WHERE id=?",
        [req.params.id]
    );

    res.send("Deleted");
});
```

---

# Better Method — Use ORM

Instead of raw SQL queries, many developers use ORM libraries.

Popular ORMs:

| ORM       | Database                   |
| --------- | -------------------------- |
| Sequelize | MySQL/PostgreSQL           |
| Prisma    | Modern SQL ORM             |
| Drizzle   | Lightweight TypeScript ORM |
| TypeORM   | Enterprise projects        |

---

# Prisma (Recommended Modern ORM)

Install:

```bash
npm install prisma @prisma/client
```

Initialize:

```bash
npx prisma init
```

Official website:

[Prisma](https://www.prisma.io/?utm_source=chatgpt.com)

---

Example Prisma schema:

```prisma
model User {
  id    Int    @id @default(autoincrement())
  name  String
  email String @unique
}
```

---

# Using PostgreSQL Instead

Connection:

```js
import pg from "pg";

const db = new pg.Pool({
    user: "postgres",
    host: "localhost",
    database: "testdb",
    password: "password",
    port: 5432,
});
```

---

# Security Important

Never do this:

```js
"SELECT * FROM users WHERE name='" + name + "'"
```

This causes SQL Injection.

Use placeholders:

```js
"SELECT * FROM users WHERE name=?"
```

---

# Recommended Stack for You

Since you're learning backend + databases + Docker/Podman:

| Purpose               | Recommendation      |
| --------------------- | ------------------- |
| Simple learning       | SQLite              |
| Real projects         | PostgreSQL          |
| Hosting compatibility | MySQL               |
| Modern development    | Prisma + PostgreSQL |

---

# Learning Roadmap

1. Learn SQL basics
2. Connect Express with database
3. CRUD operations
4. Authentication
5. Relations (One-to-Many)
6. ORM
7. Transactions
8. Docker/Podman database containers
9. Optimization & indexing

---

Useful Documentation:

* [Express.js](https://expressjs.com/?utm_source=chatgpt.com)
* [mysql2 npm package](https://www.npmjs.com/package/mysql2?utm_source=chatgpt.com)
* [node-postgres (pg)](https://node-postgres.com/?utm_source=chatgpt.com)
* [Sequelize ORM](https://sequelize.org/?utm_source=chatgpt.com)
* [Drizzle ORM](https://orm.drizzle.team/?utm_source=chatgpt.com)
