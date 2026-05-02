Good — this is exactly the kind of **real production feature** you should learn. I’ll give you a **complete working project**:

👉 Frontend (HTML + JS)
👉 Backend (Express + Multer)
👉 Upload to Cloudinary

---

# 🧱 1. Install Dependencies (Backend)

```bash
npm init -y
npm install express multer cloudinary multer-storage-cloudinary cors dotenv
```

---

# 🔐 2. Cloudinary Setup

1. Go to Cloudinary
2. Create account
3. Get:

   * `CLOUD_NAME`
   * `API_KEY`
   * `API_SECRET`

---

# 📁 Project Structure

```bash
project/
 ├── server.js
 ├── .env
 ├── public/
 │    └── index.html
```

---

# 🔑 3. .env File

```env
CLOUD_NAME=your_cloud_name
API_KEY=your_api_key
API_SECRET=your_api_secret
```

---

# ⚙️ 4. Backend (Express + Multer + Cloudinary)

```js
import express from "express";
import multer from "multer";
import cors from "cors";
import dotenv from "dotenv";
import { v2 as cloudinary } from "cloudinary";

dotenv.config();

const app = express();
app.use(cors());
app.use(express.json());
app.use(express.static("public"));

// Cloudinary config
cloudinary.config({
  cloud_name: process.env.CLOUD_NAME,
  api_key: process.env.API_KEY,
  api_secret: process.env.API_SECRET,
});

// Multer (memory storage)
const storage = multer.memoryStorage();
const upload = multer({ storage });

// Upload route
app.post("/upload", upload.single("file"), async (req, res) => {
  try {
    const file = req.file;

    // Convert buffer to base64
    const b64 = Buffer.from(file.buffer).toString("base64");
    const dataURI = `data:${file.mimetype};base64,${b64}`;

    // Upload to Cloudinary
    const result = await cloudinary.uploader.upload(dataURI, {
      resource_type: "auto", // 🔥 important for video + image
    });

    res.json({
      message: "Upload successful",
      url: result.secure_url,
      type: result.resource_type,
    });
  } catch (err) {
    res.status(500).json({ error: err.message });
  }
});

app.listen(3000, () => console.log("Server running on port 3000"));
```

---

# 🌐 5. Frontend (Photo + Video Upload)

`public/index.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>Uploader</title>
</head>
<body>

  <h2>Upload Photo or Video</h2>

  <input type="file" id="fileInput" />
  <button onclick="uploadFile()">Upload</button>

  <h3>Preview:</h3>
  <div id="preview"></div>

  <script>
    async function uploadFile() {
      const fileInput = document.getElementById("fileInput");
      const file = fileInput.files[0];

      if (!file) {
        alert("Select a file first");
        return;
      }

      const formData = new FormData();
      formData.append("file", file);

      const res = await fetch("http://localhost:3000/upload", {
        method: "POST",
        body: formData
      });

      const data = await res.json();

      const preview = document.getElementById("preview");
      preview.innerHTML = "";

      if (data.type === "image") {
        preview.innerHTML = `<img src="${data.url}" width="300"/>`;
      } else if (data.type === "video") {
        preview.innerHTML = `
          <video width="400" controls>
            <source src="${data.url}" type="video/mp4">
          </video>`;
      }
    }
  </script>

</body>
</html>
```

---

# 🔥 How It Works (IMPORTANT)

### 🧠 Flow:

1. User selects file (image/video)
2. Frontend sends `FormData`
3. Multer stores file in memory
4. Convert → base64
5. Upload to Cloudinary
6. Return URL
7. Preview on frontend

---

# ⚡ Key Concept (Very Important)

### `resource_type: "auto"`

👉 This is what allows:

* Images ✅
* Videos ✅
* PDFs ✅

Without it → video upload ❌

---

# 🧪 Test

```bash
node server.js
```

Open:

```
http://localhost:3000
```

---

# 🚀 Upgrade This (Next Level)

If you want production-level 🔥:

### Add:

* JWT authentication
* File size validation
* File type filter
* Progress bar (frontend)
* Multiple file upload
* Save URL in MongoDB

---

# ⚠️ Real Production Advice

Don’t always upload through backend ❗

👉 Better approach:

* Frontend → directly upload to Cloudinary (signed/unsigned upload)
* Backend → only generate signature

---

If you want, next step I can build for you:

👉 **Instagram-style uploader (multiple images + preview + progress + DB)**
👉 Or **Direct frontend → Cloudinary (no backend upload load)**

Just tell me 👍
