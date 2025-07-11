# 🔐 JWTAuthNodeJS
## 🛡️ Secure User Authentication using JSON Web Token (Node.js Backend)
This project demonstrates a lightweight, stateless authentication system using Node.js, Express, and JWT (JSON Web Token). Users can sign up, log in, and access protected routes. Passwords are securely hashed using bcryptjs, and users are authenticated using signed tokens stored on the client side (e.g., in local storage or cookies).

---

## 📚 Features
User Signup and Login

Secure Password Hashing using bcryptjs

Stateless JWT Token-Based Authentication

Protected API routes with middleware

Clean, modular backend logic

---

## 📦 Technologies Used
```
Node.js
Express.js
JWT (jsonwebtoken)
bcryptjs
body-parser
Postman (for testing)
```

---

## 🧠 Concepts Covered
Authentication and Authorization

Stateless Token Authentication with JWT

Password hashing with salting (bcryptjs)

Token-based route protection

JSON body parsing using body-parser

## 🛠️ Project Structure
```
JWTAuthNodeJS/
│
├── app.js               # Main server logic
├── package.json         # Dependencies
└── README.md            # This file
```

---

## 🚀 Getting Started
### 1️⃣ Prerequisites
Node.js and npm installed

Postman (for testing)

No database needed (data is stored in memory for simplicity)

### 2️⃣ Installation
```
git clone JWTAuthNodeJS.git
cd JWTAuthNodeJS
npm install
node app.js
```
---

## 🧪 How it Works
### ➕ Signup
User submits username, email, and password

Password is hashed with bcryptjs

New user is added to in-memory array

Server responds with success message

### 🔐 Login
User submits email and password

Password is verified using bcrypt.compare

If valid, server generates a signed JWT

Token is returned to the client for use in future requests

### 🔒 Accessing Protected Route
Token must be included in the request header:
Authorization: Bearer <JWT_TOKEN>
Middleware checks token validity

If valid, route is accessed

---

## 📩 API Endpoints
POST /signup
Registers a new user.

Body (JSON):
```
{
  "username": "john",
  "email": "john@example.com",
  "password": "123456"
}
```

---

POST /login
Logs in the user and returns a JWT.

Body (JSON):
```
{
  "email": "john@example.com",
  "password": "123456"
}
```
Response:

```
{
  "message": "Login successful ✅",
  "token": "<JWT_TOKEN>"
}
```
---
GET /dashboard
Accesses a protected route.

Headers:

Authorization: Bearer <JWT_TOKEN>

---

## ⚙️ Security Notes
Passwords are never stored in plain text.

JWTs are signed using a server-only secret key.

Tokens expire in 1 hour (expiresIn: '1h').

For production:

Use MongoDB for persistent user storage.

Store secrets in environment variables (.env file).

Use HTTPS.

## 🖼️ Interface Screens
Actual screenshots of each page.

<p align="center">
  <img src="https://github.com/hsj71/EncryptedPasswordMongoDB/blob/main/Screenshot%20(770).png" alt="View Merged PDFs" width="700"/>
</p>

<p align="center">
  <img src="https://github.com/hsj71/EncryptedPasswordMongoDB/blob/main/Screenshot%20(771).png" alt="View Merged PDFs" width="700"/>
</p>

<p align="center">
  <img src="https://github.com/hsj71/EncryptedPasswordMongoDB/blob/main/Screenshot%20(772).png" alt="View Merged PDFs" width="700"/>
</p>

--- 

## 🧩 Example Code (app.js)
```
const express = require("express");
const jwt = require("jsonwebtoken");
const bcrypt = require("bcryptjs");
const bodyParser = require("body-parser");

const app = express();
const PORT = 3000;
const SECRET_KEY = "supersecretkey";

app.use(bodyParser.json());

const users = [];

app.post("/signup", async (req, res) => {
  const { username, email, password } = req.body;

  const existingUser = users.find((u) => u.email === email);
  if (existingUser) return res.status(400).json({ message: "User already exists" });

  const hashedPassword = await bcrypt.hash(password, 10);
  const newUser = { id: users.length + 1, username, email, password: hashedPassword };
  users.push(newUser);

  res.status(201).json({ message: "Signup successful ✅", user: { id: newUser.id, username, email } });
});

app.post("/login", async (req, res) => {
  const { email, password } = req.body;
  const user = users.find((u) => u.email === email);
  if (!user) return res.status(400).json({ message: "User not found" });

  const isMatch = await bcrypt.compare(password, user.password);
  if (!isMatch) return res.status(401).json({ message: "Invalid password" });

  const token = jwt.sign({ id: user.id, email: user.email }, SECRET_KEY, { expiresIn: "1h" });
  res.json({ message: "Login successful ✅", token });
});

function verifyToken(req, res, next) {
  const authHeader = req.headers["authorization"];
  const token = authHeader && authHeader.split(" ")[1];
  if (!token) return res.status(403).json({ message: "No token provided" });

  jwt.verify(token, SECRET_KEY, (err, userData) => {
    if (err) return res.status(403).json({ message: "Invalid token" });
    req.user = userData;
    next();
  });
}

app.get("/dashboard", verifyToken, (req, res) => {
  res.json({
    message: `Welcome back, ${req.user.email} 🎉`,
    user: req.user
  });
});

app.listen(PORT, () => console.log(`🚀 Server running at http://localhost:${PORT}`));
```
---

## 🧼 Example Postman Test
Login:
```
POST /login
{
  "email": "john@example.com",
  "password": "123456"
}
```
Access protected route:
Header:

Authorization: Bearer <your_token_here>

---

## ✍️ Author
Made by Hrishikesh
