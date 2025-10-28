# 🚀 Express Middleware Demo

A simple and well-structured **Express.js** project demonstrating the use of **custom middleware** for logging and authentication.

---

## 📋 Overview

This project showcases two core middlewares:
1. **Request Logger Middleware** – Logs every HTTP request with a timestamp, method, and URL.  
2. **Bearer Token Authentication Middleware** – Validates requests using a `Bearer mysecrettoken` token.

It includes both **public** and **protected** routes for easy testing.

---

## 🛠️ Setup Instructions

### 1️⃣ Install Dependencies
```bash
npm init -y
npm install express
```

### 2️⃣ Create the Server File
Save the server code as `server.js` (code already provided in this repo).

### 3️⃣ Run the Server
```bash
node server.js
```

Server will start at:  
👉 `http://localhost:3000`

---

## 🧪 Testing with cURL

### ✅ Test 1: Public Route (No Authentication)
```bash
curl http://localhost:3000/public
```
**Expected Output**
```json
{
  "message": "This is a public route. No authentication required."
}
```

---

### ❌ Test 2: Protected Route (No Token)
```bash
curl http://localhost:3000/protected
```
**Expected Output**
```json
{
  "message": "Authorization header missing or incorrect"
}
```

---

### ✅ Test 3: Protected Route (Valid Token)
```bash
curl -H "Authorization: Bearer mysecrettoken" http://localhost:3000/protected
```
**Expected Output**
```json
{
  "message": "You have accessed a protected route with a valid Bearer token!"
}
```

---

### ❌ Test 4: Protected Route (Invalid Token)
```bash
curl -H "Authorization: Bearer wrongtoken" http://localhost:3000/protected
```
**Expected Output**
```json
{
  "message": "Authorization header missing or incorrect"
}
```

---

## 🧰 Testing with Postman

### 🟢 Public Route
1. Create a new **GET** request  
   URL: `http://localhost:3000/public`  
2. Click **Send**  
3. ✅ Should return **200 OK**

---

### 🔐 Protected Route (With Token)
1. Create a new **GET** request  
   URL: `http://localhost:3000/protected`  
2. Go to **Headers** tab  
   Add header:
   ```
   Key: Authorization
   Value: Bearer mysecrettoken
   ```
3. Click **Send**  
4. ✅ Should return **200 OK**

---

### 🔴 Protected Route (Without Token)
1. Create a new **GET** request  
   URL: `http://localhost:3000/protected`  
2. Don’t add any Authorization header  
3. Click **Send**  
4. ❌ Should return **401 Unauthorized**

---

## 🖥️ Example Console Output
When you run the tests, you should see logs like:
```
[2025-10-12T10:30:15.123Z] GET /public
[2025-10-12T10:30:20.456Z] GET /protected
[2025-10-12T10:30:25.789Z] GET /protected
```

---

## ⚙️ Middleware Flow

```
Request 
   ↓
Request Logger (global)
   ↓
Auth Middleware (only for protected routes)
   ↓
Route Handler
   ↓
Response
```

---

## 🧠 Key Learning Points

- **Middleware Order Matters** → Global middleware (like logging) runs first  
- **Selective Middleware** → Authentication applies only to protected routes  
- **`next()` Function** → Passes control to the next middleware or route  
- **Early Returns** → Authentication returns errors without calling `next()`  
- **Header Validation** → Shows how to extract and validate Bearer tokens  

---

## 📦 Optional: Add Scripts in `package.json`
```json
"scripts": {
  "start": "node server.js",
  "dev": "nodemon server.js"
}
```
Then run:
```bash
npm start
# or (with nodemon)
npm run dev
```

---

## 🏁 Quick Summary

| Route | Method | Auth Required | Description |
|-------|---------|----------------|--------------|
| `/` | GET | ❌ No | Welcome route with route info |
| `/public` | GET | ❌ No | Accessible without authentication |
| `/protected` | GET | ✅ Yes | Requires Bearer token: `mysecrettoken` |

---

## 🖼️ Output Screenshots

### 📌 Public Route (No Authentication)
![Public Route](OUTPUT/output%201.png)

### 📌 Protected Route (Without Token)
![Protected Route - No Token](OUTPUT/output%202.png)

### 📌 Protected Route (With Token)
![Protected Route - With Token](OUTPUT/output%203.png)
