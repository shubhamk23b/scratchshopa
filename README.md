# 🛒 Scatch — Shopping Web App

> A full-stack **e-commerce web application** built with **Node.js, Express, MongoDB and EJS**, using an MVC-style architecture with secure authentication, session-based flash messaging and product image uploads.

![Node.js](https://img.shields.io/badge/Node.js-18+-339933?logo=nodedotjs&logoColor=white)
![Express](https://img.shields.io/badge/Express-4.21-000000?logo=express&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-Mongoose_8-47A248?logo=mongodb&logoColor=white)
![EJS](https://img.shields.io/badge/EJS-3.1-B4CA65)
![JWT](https://img.shields.io/badge/Auth-JWT_+_bcrypt-000000?logo=jsonwebtokens&logoColor=white)

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Tech Stack](#️-tech-stack)
- [System Architecture](#️-system-architecture)
- [Project Structure](#-project-structure)
- [Request Lifecycle](#-request-lifecycle)
- [Authentication & Security](#-authentication--security)
- [Getting Started](#️-getting-started)
- [Environment Variables](#-environment-variables)
- [Scripts](#-scripts)
- [Security Notes](#-security-notes)
- [Future Improvements](#-future-improvements)
- [Author](#-author)

---

## 🎯 Overview

**Scatch** is a shopping website where users can browse products and shop online, built from scratch with a classic server-rendered stack. The backend follows a clean **MVC layout** — routes, controllers, models, middleware and utilities are kept in separate folders — and pages are rendered on the server with **EJS** templates and reusable partials.

---

## 🚀 Features

- 🔐 **User authentication** — registration and login with hashed passwords (**bcrypt**) and **JWT** tokens stored in cookies
- 🛡️ **Protected routes** via authentication middleware
- 🛍️ **Product catalogue** stored in **MongoDB** through Mongoose models
- 🖼️ **Product image uploads** handled by **Multer**, with photos kept in `productphotos/`
- 💬 **Flash messages** (success / error notices) using `connect-flash` and `express-session`
- 🧩 **Reusable EJS partials** for shared layout pieces (header, footer, etc.)
- ⚙️ **Environment-based configuration** using `dotenv` and `config`
- 🧱 **Modular MVC structure** that is easy to extend

<!-- TODO: confirm against the code — e.g. cart, owner/admin product creation, discounts, checkout. -->

---

## 🛠️ Tech Stack

| Category | Technology |
| -------- | ---------- |
| Runtime | Node.js |
| Web framework | Express `^4.21` |
| Database | MongoDB with Mongoose `^8.9` |
| Templating | EJS `^3.1` |
| Authentication | `jsonwebtoken`, `bcrypt`, `cookie-parser` |
| Sessions & messages | `express-session`, `connect-flash` |
| File uploads | Multer |
| Configuration | `dotenv`, `config` |
| Debugging | `debug` |

---

## 🏗️ System Architecture

```text
                       ┌───────────────────┐
                       │      Browser      │
                       └─────────┬─────────┘
                                 │  HTTP request
                                 ▼
                       ┌───────────────────┐
                       │      app.js       │  Express app, global middleware
                       │  (entry point)    │  cookie-parser · session · flash
                       └─────────┬─────────┘
                                 │
                                 ▼
                       ┌───────────────────┐
                       │      routes/      │  URL → controller mapping
                       └─────────┬─────────┘
                                 │
                                 ▼
                       ┌───────────────────┐
                       │   middleware/     │  auth checks (JWT), guards
                       └─────────┬─────────┘
                                 │
                                 ▼
                       ┌───────────────────┐
                       │   controllers/    │  business logic
                       └────┬─────────┬────┘
                            │         │
              ┌─────────────┘         └─────────────┐
              ▼                                     ▼
     ┌─────────────────┐                   ┌─────────────────┐
     │     models/     │                   │     utils/      │
     │ Mongoose schemas│                   │ helpers (tokens)│
     └────────┬────────┘                   └─────────────────┘
              │
              ▼
     ┌─────────────────┐
     │     MongoDB     │
     └─────────────────┘

        Response is rendered with  views/  +  partials/  (EJS)
        Static/product images are served from  productphotos/
        Database connection & settings live in  config/
```

---

## 📁 Project Structure

```text
scratchshopa/
│
├── app.js                # Application entry point: Express setup & middleware
├── package.json          # Dependencies and metadata
├── .gitignore
│
├── config/               # Database connection and app configuration
├── routes/               # Route definitions (URL → controller)
├── controllers/          # Request handlers / business logic
├── middleware/           # Reusable middleware (e.g. authentication guards)
├── models/               # Mongoose schemas and models
├── utils/                # Helper functions (e.g. token generation)
├── views/                # EJS page templates
├── partials/             # Reusable EJS fragments (header, footer, ...)
└── productphotos/        # Uploaded / stored product images
```

| Folder | Responsibility |
| ------ | -------------- |
| `config/` | Connects to MongoDB and holds environment/app settings |
| `routes/` | Maps HTTP endpoints to controller functions |
| `controllers/` | Contains the logic for each endpoint (register, login, product handling, ...) |
| `middleware/` | Runs before controllers — e.g. verifies the JWT and protects private pages |
| `models/` | Defines the data shape (users, products, ...) using Mongoose |
| `utils/` | Small shared helpers used across controllers |
| `views/` & `partials/` | Server-rendered HTML with EJS, split into pages and shared pieces |
| `productphotos/` | Image files for products, written by Multer |

---

## 🔄 Request Lifecycle

1. The browser sends a request, e.g. `GET /shop`.
2. **`app.js`** runs global middleware (cookie parsing, sessions, flash messages, body parsing).
3. The matching **route** is found and, for private pages, **middleware** checks the user's JWT cookie.
4. The **controller** runs: it reads/writes data through a **Mongoose model**.
5. The controller renders an **EJS view** (with partials), passing in data and any flash messages.
6. HTML is returned to the browser.

---

## 🔐 Authentication & Security

```text
 Register  ──►  password hashed with bcrypt  ──►  user saved in MongoDB
 Login     ──►  bcrypt.compare()  ──►  JWT signed  ──►  stored in cookie
 Request   ──►  middleware verifies JWT from cookie  ──►  allow / redirect
```

- Passwords are **never stored in plain text** — only bcrypt hashes.
- Sessions are tracked with signed cookies and a JWT secret loaded from environment variables.
- Flash messages report errors (invalid login, failed upload, etc.) without exposing internals.

---

## ⚙️ Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) 18 or newer
- [MongoDB](https://www.mongodb.com/) — local instance or a free [MongoDB Atlas](https://www.mongodb.com/atlas) cluster
- npm

### 1. Clone the repository

```bash
git clone https://github.com/shubhamk23b/scratchshopa.git
cd scratchshopa
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Create a `.env` file in the project root (see [below](#-environment-variables)).

### 4. Run the app

```bash
node app.js
```

Open the address printed in the terminal (check the `listen` port in `app.js`), e.g. `http://localhost:3000`.

> 💡 For auto-restart while developing: `npx nodemon app.js`

---

## 🔑 Environment Variables

Create a `.env` file (never commit it):

```env
# Server
PORT=3000
NODE_ENV=development

# Database
MONGODB_URI=mongodb://127.0.0.1:27017/scatch

# Auth
JWT_KEY=replace_with_a_long_random_string
EXPRESS_SESSION_SECRET=replace_with_another_long_random_string
```

> ⚠️ The variable names above are examples — match them to the names actually read in `config/` and `app.js`.

---

## 📜 Scripts

`package.json` currently has only a placeholder `test` script. Add a start script:

```json
"scripts": {
  "start": "node app.js",
  "dev": "nodemon app.js"
}
```

Then run `npm start` (or `npm run dev` with nodemon installed).

---

## 🔒 Security Notes

- The repo's **`.gitignore` is currently empty**. Add at least the entries below so secrets and dependencies are never committed:

  ```gitignore
  node_modules/
  .env
  ```
- Use long, random values for `JWT_KEY` and the session secret in production.
- Serve the app over **HTTPS** and set cookies as `httpOnly` / `secure` in production.
- Validate file type and size for uploaded product images.

---

## 🚀 Future Improvements

- [ ] Shopping cart persistence and checkout flow
- [ ] Payment gateway integration (Razorpay / Stripe)
- [ ] Order history and order tracking
- [ ] Admin dashboard for product management
- [ ] Search, filters and sorting
- [ ] Input validation (e.g. `express-validator`) and rate limiting
- [ ] Automated tests (Jest + Supertest)
- [ ] Cloud image storage (Cloudinary / S3)
- [ ] Docker setup and deployment (Render / Railway)

---

## 👨‍💻 Author

**Shubham Kanojiya**
AI/ML & Backend Developer

GitHub: [@shubhamk23b](https://github.com/shubhamk23b)

---

⭐ If you find this project useful, consider giving it a star on GitHub!
