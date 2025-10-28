# JWT Banking API - Complete Testing Guide

![Output 1](OUTPUT/output%201.png)
![Output 2](OUTPUT/output%202.png)
![Output 3](OUTPUT/output%203.png)
![Output 4](OUTPUT/output%204.png)
![Output 5](OUTPUT/output%205.png)

---

## Setup Instructions

### 1. Install Dependencies
```bash
npm init -y
npm install express jsonwebtoken
```

### 2. Create the Server File
Save the server code as `server.js`

### 3. Run the Server
```bash
node server.js
```

## Testing Workflow

### Step 1: Login to Get JWT Token
```bash
curl -X POST http://localhost:3000/login \
  -H "Content-Type: application/json" \
  -d '{"username": "user1", "password": "password123"}'
```
**Expected Response (200 OK):**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

Copy the token from the response — you’ll need it for the next requests!

### Step 2: Check Balance (Without Token)
```bash
curl http://localhost:3000/balance
```
**Expected Response (403 Forbidden):**
```json
{
  "message": "Invalid or expired token"
}
```

### Step 3: Check Balance (With Valid Token)
```bash
curl http://localhost:3000/balance \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```
**Expected Response (200 OK):**
```json
{
  "balance": 1000
}
```

### Step 4: Deposit Money
```bash
curl -X POST http://localhost:3000/deposit \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{"amount": 250}'
```
**Expected Response (200 OK):**
```json
{
  "message": "Deposited $250",
  "newBalance": 1250
}
```

### Step 5: Withdraw Money (Valid)
```bash
curl -X POST http://localhost:3000/withdraw \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{"amount": 150}'
```
**Expected Response (200 OK):**
```json
{
  "message": "Withdrew $150",
  "newBalance": 1100
}
```

### Step 6: Withdraw Money (Insufficient Balance)
```bash
curl -X POST http://localhost:3000/withdraw \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE" \
  -d '{"amount": 5000}'
```
**Expected Response (400 Bad Request):**
```json
{
  "message": "Insufficient balance"
}
```

## Testing with Postman
1. **Login Request**
   - Method: POST
   - URL: http://localhost:3000/login
   - Headers: Content-Type: application/json
   - Body (raw JSON):
```json
{
  "username": "user1",
  "password": "password123"
}
```
2. **Check Balance**
   - Method: GET
   - URL: http://localhost:3000/balance
   - Headers: Authorization: Bearer <your_token>
3. **Deposit Money**
   - Method: POST
   - URL: http://localhost:3000/deposit
   - Headers:
     - Content-Type: application/json
     - Authorization: Bearer <your_token>
   - Body (raw JSON):
```json
{ "amount": 250 }
```
4. **Withdraw Money**
   - Method: POST
   - URL: http://localhost:3000/withdraw
   - Headers:
     - Content-Type: application/json
     - Authorization: Bearer <your_token>
   - Body (raw JSON):
```json
{ "amount": 150 }
```

## Complete Testing Script (Bash)
Save this as `test_api.sh`:
```bash
#!/bin/bash

echo "=== Step 1: Login ==="
LOGIN_RESPONSE=$(curl -s -X POST http://localhost:3000/login \
  -H "Content-Type: application/json" \
  -d '{"username": "user1", "password": "password123"}')

echo $LOGIN_RESPONSE
TOKEN=$(echo $LOGIN_RESPONSE | jq -r '.token')
echo -e "\nToken: $TOKEN\n"

echo "=== Step 2: Check Balance (No Auth) ==="
curl -s http://localhost:3000/balance
echo -e "\n"

echo "=== Step 3: Check Balance (With Auth) ==="
curl -s http://localhost:3000/balance \
  -H "Authorization: Bearer $TOKEN"
echo -e "\n"

echo "=== Step 4: Deposit $250 ==="
curl -s -X POST http://localhost:3000/deposit \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"amount": 250}'
echo -e "\n"

echo "=== Step 5: Withdraw $150 ==="
curl -s -X POST http://localhost:3000/withdraw \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $TOKEN" \
  -d '{"amount": 150}'
echo -e "\n"

echo "=== Step 6: Check Final Balance ==="
curl -s http://localhost:3000/balance \
  -H "Authorization: Bearer $TOKEN"
echo -e "\n"
```
Run with:
```bash
bash test_api.sh
```

## Error Scenarios to Test
- Invalid Credentials → 401 Unauthorized
- Missing Token → 403 Forbidden
- Invalid Token → 403 Forbidden
- Negative Deposit Amount → 400 Bad Request
- Insufficient Balance → 400 Bad Request

## How JWT Authentication Works
### 1. Login Flow
- Client → POST /login (username, password)
- Server → Validates credentials
- Server → Generates JWT token
- Server → Returns token to client

### 2. Protected Route Flow
- Client → GET /balance with Authorization: Bearer <token>
- Server → Verifies token
- Server → Grants access

### 3. JWT Token Structure
Header.Payload.Signature

## Key Learning Points
- Token-based stateless authentication
- Protected routes require valid JWT
- Token expiration (1 hour default)
- Error handling and HTTP status codes

## Security Best Practices (Production)
✅ Use .env for secrets  
✅ Enable HTTPS  
✅ Use bcrypt for password hashing  
✅ Short token lifetimes  
✅ Implement refresh tokens  
✅ Add rate limiting  
✅ Validate all inputs

## Package.json Configuration
```json
{
  "name": "jwt-banking-api",
  "version": "1.0.0",
  "description": "Banking API with JWT Authentication",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  },
  "dependencies": {
    "express": "^4.18.2",
    "jsonwebtoken": "^9.0.2"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

## Test Credentials
- Username: user1
- Password: password123
- Initial Balance: $1000


