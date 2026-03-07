# learn-express-js
Here’s a **start-to-end Express.js guide** with **code** and a **real project use case**.

**Express.js** is a minimal web framework for Node.js, used to build servers, APIs, middleware pipelines, and full backend apps. As of now, the npm package lists **Express 5.2.1**, and Express 5 changed some routing and migration behavior compared with older tutorials, so it is better to learn with the newer style. Node.js supports both **ES modules (`import`)** and **CommonJS (`require`)**. ([npm][1])

---

# 1) What Express is used for

You use Express to build:

* REST APIs
* Authentication systems
* Backend for React/Next apps
* File upload services
* Admin dashboards
* CRUD apps
* Middleware-based server logic

Express is popular because it gives you a thin layer over Node’s HTTP server, plus routing, middleware, request/response helpers, static file serving, and error handling. ([Express][2])

---

# 2) Prerequisites

You should know:

* Basic JavaScript
* Objects, arrays, functions
* `async/await`
* JSON
* Basic HTTP methods: `GET`, `POST`, `PUT`, `PATCH`, `DELETE`

---

# 3) Setup Express project

We will use **ES Modules**.

## Create project

```bash
mkdir express-start-to-end
cd express-start-to-end
npm init -y
npm install express
```

Then in `package.json` add:

```json
{
  "name": "express-start-to-end",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "node server.js"
  },
  "dependencies": {
    "express": "^5.2.1"
  }
}
```

Node treats files as ES modules when `"type": "module"` is set in `package.json`. ([Node.js][3])

---

# 4) First Express server

Create `server.js`

```js
import express from "express";

const app = express();
const PORT = 3000;

app.get("/", (req, res) => {
  res.send("Hello from Express!");
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

Run:

```bash
npm run dev
```

Open:

```bash
http://localhost:3000
```

This is the standard pattern: create app, define routes, start server. ([Express][2])

---

# 5) Understanding the core flow

Every Express app is built around:

* `app`
* `req` → request
* `res` → response
* middleware
* routes

Example:

```js
app.get("/about", (req, res) => {
  res.send("About page");
});
```

* `app.get()` means respond to GET request
* `"/about"` is route path
* `(req, res)` is handler function

---

# 6) HTTP methods in Express

```js
app.get("/users", (req, res) => {
  res.send("Get all users");
});

app.post("/users", (req, res) => {
  res.send("Create user");
});

app.put("/users/1", (req, res) => {
  res.send("Replace user");
});

app.patch("/users/1", (req, res) => {
  res.send("Update part of user");
});

app.delete("/users/1", (req, res) => {
  res.send("Delete user");
});
```

---

# 7) Sending responses

## `res.send()`

```js
app.get("/send", (req, res) => {
  res.send("Simple text");
});
```

## `res.json()`

```js
app.get("/json", (req, res) => {
  res.json({
    success: true,
    message: "JSON response"
  });
});
```

## `res.status()`

```js
app.get("/notfound", (req, res) => {
  res.status(404).json({ error: "Not found" });
});
```

---

# 8) Route parameters

```js
app.get("/users/:id", (req, res) => {
  const userId = req.params.id;
  res.send(`User ID is ${userId}`);
});
```

Request:

```bash
GET /users/25
```

Response:

```bash
User ID is 25
```

---

# 9) Query parameters

```js
app.get("/search", (req, res) => {
  const { term, page } = req.query;

  res.json({
    term,
    page
  });
});
```

Request:

```bash
GET /search?term=phone&page=2
```

Response:

```json
{
  "term": "phone",
  "page": "2"
}
```

---

# 10) Middleware

Middleware is one of the most important concepts in Express. It runs between request and response. Express has official guides for writing and using middleware. ([Express][2])

## Example custom middleware

```js
function logger(req, res, next) {
  console.log(`${req.method} ${req.url}`);
  next();
}

app.use(logger);
```

Now every request will be logged.

## Why `next()` matters

If middleware does not call `next()`, the request stops there.

Example:

```js
function blockRequest(req, res, next) {
  return res.status(403).json({ message: "Blocked" });
}
```

This middleware ends the request.

---

# 11) Built-in middleware

## Parse JSON body

```js
app.use(express.json());
```

Now Express can read JSON request bodies.

Example:

```js
app.post("/products", (req, res) => {
  console.log(req.body);
  res.json({
    received: req.body
  });
});
```

Send:

```json
{
  "name": "Keyboard",
  "price": 1200
}
```

## Parse form data

```js
app.use(express.urlencoded({ extended: true }));
```

---

# 12) Static files

You can serve CSS, JS, images, HTML from a folder using `express.static`. This is part of Express getting started docs. ([Express][2])

```js
app.use(express.static("public"));
```

Project:

```bash
project/
  server.js
  public/
    index.html
    style.css
```

If `public/index.html` exists, opening `/index.html` serves it directly.

---

# 13) Routing structure

As apps grow, routes should be moved into separate files.

## `routes/userRoutes.js`

```js
import express from "express";

const router = express.Router();

router.get("/", (req, res) => {
  res.json([{ id: 1, name: "Seyam" }]);
});

router.get("/:id", (req, res) => {
  res.json({ id: req.params.id, name: "Single user" });
});

export default router;
```

## `server.js`

```js
import express from "express";
import userRoutes from "./routes/userRoutes.js";

const app = express();

app.use(express.json());
app.use("/users", userRoutes);

app.listen(3000, () => {
  console.log("Server running on http://localhost:3000");
});
```

Now:

* `/users`
* `/users/1`

---

# 14) Express Router

`express.Router()` helps split routes into modules. This is the standard pattern for real apps. ([Express][2])

Example:

```js
const router = express.Router();
```

Think of router as a mini Express app.

---

# 15) Request body, params, query difference

```js
app.post("/orders/:id", (req, res) => {
  console.log("params:", req.params); // route params
  console.log("query:", req.query);   // ?sort=asc
  console.log("body:", req.body);     // JSON body

  res.json({ ok: true });
});
```

Request example:

```bash
POST /orders/10?sort=asc
```

Body:

```json
{
  "status": "paid"
}
```

---

# 16) Error handling

Express has dedicated error-handling middleware. ([Express][2])

## Normal middleware

```js
app.use((req, res, next) => {
  next();
});
```

## Error middleware

It has **4 parameters**:

```js
app.use((err, req, res, next) => {
  res.status(500).json({
    success: false,
    message: err.message
  });
});
```

## Example

```js
app.get("/boom", (req, res, next) => {
  try {
    throw new Error("Something went wrong");
  } catch (err) {
    next(err);
  }
});
```

---

# 17) 404 handler

```js
app.use((req, res) => {
  res.status(404).json({
    success: false,
    message: "Route not found"
  });
});
```

Place this **after all routes**.

---

# 18) Project structure for real apps

A clean beginner-friendly structure:

```bash
express-project/
  controllers/
    productController.js
  routes/
    productRoutes.js
  middleware/
    errorHandler.js
    logger.js
  utils/
  public/
  server.js
  package.json
```

For larger apps:

```bash
src/
  config/
  controllers/
  services/
  models/
  routes/
  middleware/
  validators/
  app.js
  server.js
```

---

# 19) Real project use case: Product Store API

Now let’s build a real mini backend.

## Features

* Get all products
* Get single product
* Create product
* Update product
* Delete product
* Validation
* Error handling
* Middleware
* Modular structure

---

## Step 1: folder structure

```bash
product-store-api/
  controllers/
    productController.js
  routes/
    productRoutes.js
  middleware/
    logger.js
    notFound.js
    errorHandler.js
  data/
    products.js
  server.js
  package.json
```

---

## Step 2: `package.json`

```json
{
  "name": "product-store-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "node server.js"
  },
  "dependencies": {
    "express": "^5.2.1"
  }
}
```

---

## Step 3: `data/products.js`

```js
export const products = [
  { id: 1, name: "Keyboard", price: 1200, inStock: true },
  { id: 2, name: "Mouse", price: 800, inStock: true },
  { id: 3, name: "Monitor", price: 15000, inStock: false }
];
```

---

## Step 4: `middleware/logger.js`

```js
export function logger(req, res, next) {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.url}`);
  next();
}
```

---

## Step 5: `middleware/notFound.js`

```js
export function notFound(req, res, next) {
  res.status(404).json({
    success: false,
    message: `Cannot find ${req.originalUrl}`
  });
}
```

---

## Step 6: `middleware/errorHandler.js`

```js
export function errorHandler(err, req, res, next) {
  console.error(err);

  res.status(err.status || 500).json({
    success: false,
    message: err.message || "Internal Server Error"
  });
}
```

---

## Step 7: `controllers/productController.js`

```js
import { products } from "../data/products.js";

// GET /api/products
export function getAllProducts(req, res) {
  const { inStock } = req.query;

  if (inStock === "true") {
    return res.json(products.filter(product => product.inStock));
  }

  if (inStock === "false") {
    return res.json(products.filter(product => !product.inStock));
  }

  res.json(products);
}

// GET /api/products/:id
export function getProductById(req, res, next) {
  const id = Number(req.params.id);
  const product = products.find(product => product.id === id);

  if (!product) {
    const err = new Error("Product not found");
    err.status = 404;
    return next(err);
  }

  res.json(product);
}

// POST /api/products
export function createProduct(req, res, next) {
  const { name, price, inStock } = req.body;

  if (!name || typeof price !== "number") {
    const err = new Error("Name and numeric price are required");
    err.status = 400;
    return next(err);
  }

  const newProduct = {
    id: products.length ? products[products.length - 1].id + 1 : 1,
    name,
    price,
    inStock: typeof inStock === "boolean" ? inStock : true
  };

  products.push(newProduct);

  res.status(201).json({
    success: true,
    product: newProduct
  });
}

// PUT /api/products/:id
export function updateProduct(req, res, next) {
  const id = Number(req.params.id);
  const product = products.find(product => product.id === id);

  if (!product) {
    const err = new Error("Product not found");
    err.status = 404;
    return next(err);
  }

  const { name, price, inStock } = req.body;

  if (name !== undefined) product.name = name;
  if (price !== undefined) product.price = price;
  if (inStock !== undefined) product.inStock = inStock;

  res.json({
    success: true,
    product
  });
}

// DELETE /api/products/:id
export function deleteProduct(req, res, next) {
  const id = Number(req.params.id);
  const index = products.findIndex(product => product.id === id);

  if (index === -1) {
    const err = new Error("Product not found");
    err.status = 404;
    return next(err);
  }

  const deleted = products.splice(index, 1);

  res.json({
    success: true,
    deleted: deleted[0]
  });
}
```

---

## Step 8: `routes/productRoutes.js`

```js
import express from "express";
import {
  getAllProducts,
  getProductById,
  createProduct,
  updateProduct,
  deleteProduct
} from "../controllers/productController.js";

const router = express.Router();

router.get("/", getAllProducts);
router.get("/:id", getProductById);
router.post("/", createProduct);
router.put("/:id", updateProduct);
router.delete("/:id", deleteProduct);

export default router;
```

---

## Step 9: `server.js`

```js
import express from "express";
import productRoutes from "./routes/productRoutes.js";
import { logger } from "./middleware/logger.js";
import { notFound } from "./middleware/notFound.js";
import { errorHandler } from "./middleware/errorHandler.js";

const app = express();
const PORT = 3000;

app.use(express.json());
app.use(logger);

app.get("/", (req, res) => {
  res.send("Welcome to Product Store API");
});

app.use("/api/products", productRoutes);

app.use(notFound);
app.use(errorHandler);

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

---

# 20) Testing the real project

## Get all products

```bash
GET http://localhost:3000/api/products
```

## Get one product

```bash
GET http://localhost:3000/api/products/1
```

## Filter products

```bash
GET http://localhost:3000/api/products?inStock=true
```

## Create product

```bash
POST http://localhost:3000/api/products
Content-Type: application/json
```

Body:

```json
{
  "name": "Laptop Stand",
  "price": 2500,
  "inStock": true
}
```

## Update product

```bash
PUT http://localhost:3000/api/products/2
Content-Type: application/json
```

Body:

```json
{
  "price": 950,
  "inStock": false
}
```

## Delete product

```bash
DELETE http://localhost:3000/api/products/3
```

---

# 21) Real-world improvement: validation

In real projects, you should validate input properly. A common middleware package is `express-validator`, which is actively maintained on npm. ([npm][4])

Install:

```bash
npm install express-validator
```

Example:

```js
import { body, validationResult } from "express-validator";

export const validateProduct = [
  body("name").trim().notEmpty().withMessage("Name is required"),
  body("price").isNumeric().withMessage("Price must be a number"),
  (req, res, next) => {
    const errors = validationResult(req);

    if (!errors.isEmpty()) {
      return res.status(400).json({
        success: false,
        errors: errors.array()
      });
    }

    next();
  }
];
```

Use it:

```js
router.post("/", validateProduct, createProduct);
```

---

# 22) Real-world improvement: rate limiting

For login, OTP, password reset, or public APIs, rate limiting is important. `express-rate-limit` is a current package used for this purpose. ([npm][5])

Install:

```bash
npm install express-rate-limit
```

Example:

```js
import rateLimit from "express-rate-limit";

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  limit: 100,
  message: {
    success: false,
    message: "Too many requests, try again later"
  }
});

app.use(limiter);
```

---

# 23) Real-world improvement: sessions

If you build server-rendered apps or admin dashboards, sessions are common. `express-session` is still maintained on npm. ([npm][6])

Install:

```bash
npm install express-session
```

Example:

```js
import session from "express-session";

app.use(session({
  secret: "supersecretkey",
  resave: false,
  saveUninitialized: false
}));
```

---

# 24) Security basics

Express security guidance says to keep both **Express** and **Node.js** updated, because Node vulnerabilities affect Express apps too. The Express site also publishes security updates, and the project recommended upgrading Multer after a high-severity DoS issue in 2025. ([Express][7])

Basic security habits:

* keep dependencies updated
* validate input
* use rate limiting
* never trust user input
* hide stack traces in production
* use HTTPS in deployment
* sanitize uploaded files
* use auth and authorization checks

---

# 25) Express 5 note you should know

A lot of older YouTube tutorials are based on Express 4. In Express 5, some routing and legacy APIs changed, and the Express docs explicitly warn that older **string pattern routes** no longer work the same way. If you follow older tutorials exactly, some examples may break. ([Express][8])

So prefer modern route definitions like:

```js
app.get("/products/:id", handler);
```

instead of relying on older pattern tricks.

---

# 26) Difference between Express and Node HTTP

## Node HTTP

```js
import http from "http";

const server = http.createServer((req, res) => {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end("Hello");
});

server.listen(3000);
```

## Express

```js
import express from "express";

const app = express();

app.get("/", (req, res) => {
  res.send("Hello");
});

app.listen(3000);
```

Express is easier because it gives you:

* routing
* middleware
* params/query/body helpers
* better response methods
* app structure

---

# 27) Common beginner mistakes

## 1. Forgetting `express.json()`

Then `req.body` becomes undefined for JSON requests.

## 2. Wrong route order

If 404 middleware is placed too early, real routes won’t run.

## 3. Not calling `next()`

Middleware can hang request flow.

## 4. Using string instead of number

Route params and query values are strings by default.

```js
const id = Number(req.params.id);
```

## 5. Mixing CommonJS and ESM incorrectly

Node supports both, but your project setup must be consistent. ([Node.js][3])

---

# 28) Best practice version of server starter

This is a very good starter template:

```js
import express from "express";

const app = express();
const PORT = process.env.PORT || 3000;

// built-in middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// custom middleware
app.use((req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
});

// routes
app.get("/", (req, res) => {
  res.json({ message: "API is running" });
});

app.get("/api/health", (req, res) => {
  res.json({
    success: true,
    uptime: process.uptime()
  });
});

// 404
app.use((req, res) => {
  res.status(404).json({ message: "Route not found" });
});

// error handler
app.use((err, req, res, next) => {
  res.status(500).json({
    message: err.message || "Server error"
  });
});

app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

---

# 29) What to learn after this

After basic Express, learn these in order:

1. MongoDB or PostgreSQL
2. MVC structure
3. authentication with JWT/session
4. file upload
5. cookies
6. CORS
7. environment variables
8. deployment
9. testing APIs
10. WebSocket / real-time apps

---

# 30) Mini roadmap

## Beginner

* create server
* routes
* req/res
* middleware
* params/query/body
* JSON responses
* 404 and error handling

## Intermediate

* router modules
* controllers
* validation
* auth
* database integration
* file upload
* pagination/filter/search

## Advanced

* caching
* rate limiting
* logging
* production security
* testing
* microservices
* deployment and scaling

---

# 31) Final interview-style summary

**Express app lifecycle:**

1. client sends request
2. Express receives request
3. middleware runs
4. matching route runs
5. response is sent
6. if error happens, error middleware handles it

**Most important Express topics:**

* app setup
* routing
* middleware
* JSON parsing
* modular routes
* error handling
* CRUD APIs
* validation
* security basics

---

# 32) One more real project idea for practice

Build one of these:

* **Student Management API**

  * add student
  * update student
  * list students
  * search by department

* **Bakery Order API**

  * create cake order
  * update status
  * list orders
  * calculate total price

* **Auth API**

  * register
  * login
  * logout
  * protected profile route

Since you mentioned real project use case, a **Bakery Order API** would actually be a very practical Express project for you.

---

If you want, I can now give you **Part 2: Express + MongoDB full project**, or **a complete Bakery Management API in Express with folder structure and code**.

[1]: https://www.npmjs.com/package/express?utm_source=chatgpt.com "Express"
[2]: https://expressjs.com/2026/02/27/security-releases.html?utm_source=chatgpt.com "February 2026 Security Releases"
[3]: https://nodejs.org/api/esm.html?utm_source=chatgpt.com "ECMAScript modules | Node.js v25.8.0 Documentation"
[4]: https://www.npmjs.com/package/express-validator?utm_source=chatgpt.com "express-validator"
[5]: https://www.npmjs.com/package/express-rate-limit?utm_source=chatgpt.com "express-rate-limit"
[6]: https://www.npmjs.com/package/express-session?utm_source=chatgpt.com "express-session"
[7]: https://expressjs.com/en/advanced/security-updates.html?utm_source=chatgpt.com "Security updates"
[8]: https://expressjs.com/en/guide/migrating-5.html?utm_source=chatgpt.com "Migrating to Express 5"
