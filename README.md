# Study Share App 📚

A full-stack educational resource-sharing web app. Users can register, log in, upload
NCERT PDFs / TXT files / PNG images (max 10MB) tagged to one of 5 subjects
(**Maths, Science, Hindi, Sanskrit, SST**), and download, like, and comment on
materials shared by others.

## Tech Stack

- **Backend:** Node.js, Express
- **Database:** MongoDB with Mongoose
- **Auth:** JWT (sent via `Authorization: Bearer` header and an httpOnly cookie), bcrypt password hashing
- **File uploads:** Multer, with server-side MIME + extension + 10MB size validation
- **Frontend:** Plain HTML + Tailwind CSS (via CDN) + vanilla JS (fetch API)

## Project Structure

```
study-share-app/
├── config/db.js              # MongoDB connection
├── controllers/               # authController.js, fileController.js
├── models/                    # User.js, File.js
├── middleware/                 # auth.js (JWT), upload.js (Multer)
├── routes/                     # authRoutes.js, fileRoutes.js
├── public/                     # css/, js/, uploads/ (stored files)
├── views/                      # index.html, login.html, register.html
├── .env                        # Environment variables
└── server.js                   # App entry point
```

## Setup

1. **Install dependencies**
   ```bash
   cd study-share-app
   npm install
   ```

2. **Configure environment variables** — edit `.env`:
   ```
   PORT=5000
   MONGO_URI=mongodb://127.0.0.1:27017/study-share-app
   JWT_SECRET=change_this_to_a_long_random_secret_string
   JWT_EXPIRES_IN=7d
   ```
   Point `MONGO_URI` at a local MongoDB instance or a MongoDB Atlas connection string.
   **Change `JWT_SECRET` to a long, random value before deploying.**

3. **Run MongoDB** (if running locally)
   ```bash
   mongod
   ```

4. **Start the server**
   ```bash
   npm start        # production
   npm run dev       # development, with nodemon auto-reload
   ```

5. Open **http://localhost:5000** — you'll land on the dashboard, which redirects
   to `/login` if you're not authenticated. Register a new account, then log in.

## API Overview

| Method | Route                          | Description                          | Auth |
|--------|---------------------------------|---------------------------------------|------|
| POST   | `/api/auth/register`            | Create an account                     | No   |
| POST   | `/api/auth/login`               | Log in, returns JWT                   | No   |
| POST   | `/api/auth/logout`               | Clear auth cookie                     | Yes  |
| GET    | `/api/auth/me`                   | Get current user                      | Yes  |
| GET    | `/api/files?subject=Maths`       | List files (optional subject filter)  | Yes  |
| POST   | `/api/files/upload`              | Upload a file (multipart/form-data)   | Yes  |
| GET    | `/api/files/:id`                 | Get a single file's metadata          | Yes  |
| GET    | `/api/files/:id/download`        | Download the file                     | Yes  |
| PUT    | `/api/files/:id/like`            | Toggle like on a file                 | Yes  |
| POST   | `/api/files/:id/comments`        | Add a comment                         | Yes  |
| GET    | `/api/files/:id/comments`        | List comments                         | Yes  |

## Notes on Security & Validation

- Passwords are hashed with **bcrypt** (10 salt rounds) before being stored.
- Uploads are restricted to `application/pdf`, `text/plain`, and `image/png`
  (checked by both MIME type and file extension) and capped at **10MB** via Multer's `limits`.
- Stored filenames are randomized (timestamp + random hex) to avoid collisions and
  path traversal issues; the original filename is preserved separately for downloads.
- All `/api/files/*` routes require a valid JWT.

---

Made by **Atharva Phadnis**
