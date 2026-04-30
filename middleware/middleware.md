“**All middlewares**” doesn’t really exist in Express—real projects use a **combination of built-in, third-party, and custom middlewares** depending on the app. I’ll give you a **production-level breakdown** with clear syntax and when to use each.

---

# 🔥 1. Built-in Express Middlewares

## 1. `express.json()`

👉 Parses JSON request body

```js
app.use(express.json({ limit: "16kb" }));
```

**Why use:**

* API receives JSON data
* Limit prevents large payload attacks

---

## 2. `express.urlencoded()`

👉 Parses form data (HTML forms)

```js
app.use(express.urlencoded({ extended: true }));
```

**Options:**

* `extended: true` → allows nested objects (recommended)

---

## 3. `express.static()`

👉 Serve static files (images, CSS, JS)

```js
app.use(express.static("public"));
```

or

```js
app.use("/static", express.static("public"));
```

---

## 4. `express.Router()`

👉 Modular route handling

```js
const router = express.Router();

router.get("/users", (req, res) => {
  res.send("Users list");
});

app.use("/api", router);
```

---

# 🚀 2. Core Third-Party Middlewares (Used in Production)

---

## 5. CORS → `cors`

👉 Handle cross-origin requests

```js
import cors from "cors";

app.use(cors({
  origin: "http://localhost:3000",
  credentials: true
}));
```

---

## 6. Logger → `morgan`

👉 Logs HTTP requests

```js
import morgan from "morgan";

app.use(morgan("dev"));
```

**Formats:**

* `"dev"` (short)
* `"combined"` (production logs)

---

## 7. Security Headers → `helmet`

👉 Protect app from common attacks

```js
import helmet from "helmet";

app.use(helmet());
```

---

## 8. Rate Limiting → `express-rate-limit`

👉 Prevent brute-force attacks

```js
import rateLimit from "express-rate-limit";

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 min
  max: 100
});

app.use(limiter);
```

---

## 9. Cookie Parser → `cookie-parser`

```js
import cookieParser from "cookie-parser";

app.use(cookieParser());
```

---

## 10. Session → `express-session`

```js
import session from "express-session";

app.use(session({
  secret: "secret-key",
  resave: false,
  saveUninitialized: false,
  cookie: { secure: false }
}));
```

---

## 11. Body Validation → `express-validator`

```js
import { body, validationResult } from "express-validator";

app.post(
  "/user",
  body("email").isEmail(),
  (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json(errors);
    }
    res.send("Valid data");
  }
);
```

---

## 12. File Upload → `multer`

```js
import multer from "multer";

const upload = multer({ dest: "uploads/" });

app.post("/upload", upload.single("file"), (req, res) => {
  res.send("File uploaded");
});
```

---

## 13. Compression → `compression`

👉 Reduce response size

```js
import compression from "compression";

app.use(compression());
```

---

## 14. MongoDB Sanitization → `express-mongo-sanitize`

```js
import mongoSanitize from "express-mongo-sanitize";

app.use(mongoSanitize());
```

---

## 15. XSS Protection → `xss-clean`

```js
import xss from "xss-clean";

app.use(xss());
```

---

# 🧠 3. Custom Middlewares (VERY IMPORTANT)

---

## 16. Basic Custom Middleware

```js
const logger = (req, res, next) => {
  console.log(`${req.method} ${req.url}`);
  next();
};

app.use(logger);
```

---

## 17. Authentication Middleware (JWT)

```js
import jwt from "jsonwebtoken";

const verifyJWT = (req, res, next) => {
  const token = req.headers.authorization;

  if (!token) {
    return res.status(401).send("No token");
  }

  try {
    const decoded = jwt.verify(token, "secret");
    req.user = decoded;
    next();
  } catch (err) {
    res.status(403).send("Invalid token");
  }
};

app.use("/protected", verifyJWT);
```

---

## 18. Role-Based Authorization

```js
const authorizeRoles = (...roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).send("Access denied");
    }
    next();
  };
};

app.get("/admin", verifyJWT, authorizeRoles("admin"), (req, res) => {
  res.send("Admin panel");
});
```

---

## 19. Error Handling Middleware

⚠️ Must have 4 parameters

```js
const errorHandler = (err, req, res, next) => {
  res.status(500).json({
    message: err.message
  });
};

app.use(errorHandler);
```

---

## 20. Async Error Wrapper

```js
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};
```

Usage:

```js
app.get("/data", asyncHandler(async (req, res) => {
  const data = await fetchData();
  res.json(data);
}));
```

---

# 🏗️ 4. Production-Level Middleware Stack (Real Example)

```js
app.use(express.json({ limit: "16kb" }));
app.use(express.urlencoded({ extended: true }));

app.use(cors());
app.use(helmet());
app.use(morgan("combined"));
app.use(cookieParser());
app.use(compression());
app.use(mongoSanitize());
app.use(xss());

app.use("/api", router);

app.use(errorHandler);
```

---

# ⚡ Key Concepts You MUST Understand

### Middleware Flow:

```txt
Request → Middleware → Middleware → Route → Response
```

### Important Rules:

* Always call `next()` unless ending response
* Order matters (very important in production)
* Error middleware comes last

---

# 🎯 Pro Tip (Real Industry Insight)

In real-world apps:

* You **don’t use all middlewares**
* You choose based on:

  * Security needs
  * Performance
  * Project size

---

If you want, I can next:
✅ Build a **complete production backend structure (MVC + middleware setup)**
✅ Or explain **middleware interview questions (very important for jobs)**
