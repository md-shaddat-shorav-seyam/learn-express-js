Alright — here’s a **complete, real-world Multer tutorial** (from basics → production use). I’ll keep it practical so you can actually use it in your projects.

---

# 🚀 What is Multer?

**Multer** is a middleware for **handling `multipart/form-data`**, mainly used for **file uploads** in Express apps.

👉 Without Multer:

* `express.json()` ❌ cannot handle files

👉 With Multer:

* Upload images, PDFs, videos, etc. ✅

---

# 📦 Install Multer

```bash
npm install multer
```

---

# ⚙️ Basic Setup (Single File Upload)

### 📁 Folder structure

```
project/
 ├── uploads/
 ├── app.js
```

### 🧠 Code

```js
import express from "express";
import multer from "multer";

const app = express();

// Storage config
const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, "uploads/");
  },
  filename: function (req, file, cb) {
    cb(null, Date.now() + "-" + file.originalname);
  }
});

const upload = multer({ storage });

// Route
app.post("/upload", upload.single("file"), (req, res) => {
  res.send("File uploaded successfully");
});

app.listen(3000, () => console.log("Server running"));
```

---

# 🔥 How it works

### `upload.single("file")`

* Accepts **1 file**
* `"file"` = name of input field (important)

---

# 🧪 HTML Test Form

```html
<form action="http://localhost:3000/upload" method="POST" enctype="multipart/form-data">
  <input type="file" name="file" />
  <button type="submit">Upload</button>
</form>
```

---

# 📂 Access Uploaded File

Inside route:

```js
console.log(req.file);
```

Example output:

```js
{
  fieldname: 'file',
  originalname: 'photo.png',
  encoding: '7bit',
  mimetype: 'image/png',
  destination: 'uploads/',
  filename: '1699999999-photo.png',
  path: 'uploads/1699999999-photo.png',
  size: 12345
}
```

---

# 📌 Multiple Upload Methods

## 1. Single File

```js
upload.single("avatar")
```

## 2. Multiple Files (same field)

```js
upload.array("photos", 5)
```

## 3. Multiple Fields

```js
upload.fields([
  { name: "avatar", maxCount: 1 },
  { name: "gallery", maxCount: 5 }
])
```

---

# 🎯 File Filtering (Security)

```js
const fileFilter = (req, file, cb) => {
  if (file.mimetype.startsWith("image")) {
    cb(null, true);
  } else {
    cb(new Error("Only images allowed"), false);
  }
};

const upload = multer({ storage, fileFilter });
```

---

# 📏 File Size Limit

```js
const upload = multer({
  storage,
  limits: { fileSize: 2 * 1024 * 1024 } // 2MB
});
```

---

# ❌ Error Handling

```js
app.post("/upload", (req, res) => {
  upload.single("file")(req, res, function (err) {
    if (err) {
      return res.status(400).send(err.message);
    }
    res.send("Uploaded!");
  });
});
```

---

# 🧠 Real Production Example (User Profile Upload)

### Use case:

* Upload user profile picture
* Save path in DB

```js
app.post("/profile", upload.single("avatar"), async (req, res) => {
  const user = {
    name: req.body.name,
    avatar: req.file.path
  };

  // Save to DB
  res.json(user);
});
```

---

# ☁️ Upload to Cloud (Cloudinary Example)

👉 Instead of saving locally

```bash
npm install cloudinary multer-storage-cloudinary
```

```js
import { CloudinaryStorage } from "multer-storage-cloudinary";
import cloudinary from "cloudinary";

const storage = new CloudinaryStorage({
  cloudinary,
  params: {
    folder: "uploads",
  },
});

const upload = multer({ storage });
```

---

# ⚡ Memory Storage (Buffer)

```js
const storage = multer.memoryStorage();

const upload = multer({ storage });
```

👉 Useful for:

* Sending file directly to AWS S3
* Processing image before saving

---

# 🔐 Best Practices (IMPORTANT)

### ✅ Always validate:

* File type
* File size

### ❌ Never trust:

* `file.originalname`

### ✅ Use:

* Unique filenames (UUID / timestamp)

### ✅ Store:

* Only file path in DB (not full file)

---

# ⚠️ Common Mistakes

❌ Wrong field name

```js
upload.single("file")
```

HTML must match:

```html
<input name="file" />
```

---

❌ Missing `enctype`

```html
enctype="multipart/form-data"
```

---

❌ Not creating uploads folder

```bash
mkdir uploads
```

---

# 🧩 Advanced Middleware Chain Example

```js
app.post(
  "/upload",
  authMiddleware,
  upload.single("file"),
  validateFile,
  controller
);
```

---

# 🎯 When to Use Multer in Real Life

* User profile images
* Blog image uploads
* E-commerce product images
* Resume upload (PDF)
* Chat apps (file sharing)

---

# 🧠 Pro Tip (Important for You 🔥)

Since you're learning **backend + real-world apps**, combine Multer with:

* MongoDB (store file path)
* JWT auth (secure upload)
* Cloud storage (Cloudinary / AWS S3)

---

If you want next level 🔥:
👉 I can build a **full project (Multer + Express + MongoDB + JWT)** like:

* Instagram-style image upload
* File upload API with authentication

Just tell me 👍
