# Banking Transfer System with MongoDB

## Output Screenshots
![Output 1](/OUTPUT/output%201.png)  
![Output 2](/OUTPUT/output%202.png)  
![Output 3](/OUTPUT/output%203.png)

A Node.js banking transfer system using **Express** and **MongoDB**, with complete balance validation, error handling, and testing guide.

---

## Features

- **Create Users**: Initialize accounts (Alice and Bob) with predefined balances.  
- **Transfer Money**: Send money between accounts with validation checks.  
- **View Users**: See all accounts and balances.  
- **Error Handling**: Handles insufficient balance, invalid user IDs, missing fields, negative amounts, and self-transfers.  

---

## Prerequisites

### 1. Install Dependencies
```bash
npm init -y
npm install express mongoose
```

### 2. Install and Start MongoDB

**macOS (Homebrew):**
```bash
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community
```

**Ubuntu/Debian:**
```bash
sudo apt-get install mongodb
sudo systemctl start mongodb
```

**Windows:**
- Download MongoDB Community Server from [mongodb.com](https://www.mongodb.com/try/download/community)
- Install and start MongoDB service

**Verify MongoDB is running:**
```bash
mongosh
# or
mongo
```

### 3. Start the Server
```bash
node server.js
```

---

## API Endpoints

### 1. Create Users
```bash
POST /create-users
```
**Expected Response (201 Created):**
```json
{
  "message": "Users created",
  "users": [
    { "name": "Alice", "balance": 1000, "_id": "alice_id", "__v": 0 },
    { "name": "Bob", "balance": 500, "_id": "bob_id", "__v": 0 }
  ]
}
```
📝 Save `_id` values for transfers.

---

### 2. View Users
```bash
GET /users
```
**Response:**
```json
{
  "users": [
    { "_id": "alice_id", "name": "Alice", "balance": 1000, "__v": 0 },
    { "_id": "bob_id", "name": "Bob", "balance": 500, "__v": 0 }
  ]
}
```

---

### 3. Transfer Money

**Successful Transfer (Alice → Bob: $150):**
```bash
POST /transfer
Content-Type: application/json

{
  "fromUserId": "alice_id",
  "toUserId": "bob_id",
  "amount": 150
}
```
**Expected Response (200 OK):**
```json
{
  "message": "Transferred $150 from Alice to Bob",
  "senderBalance": 850,
  "receiverBalance": 650
}
```

**Failed Transfer (Insufficient Balance):**
```bash
POST /transfer
Content-Type: application/json

{
  "fromUserId": "bob_id",
  "toUserId": "alice_id",
  "amount": 900
}
```
**Expected Response (400 Bad Request):**
```json
{
  "message": "Insufficient balance"
}
```

---

## Complete Testing Script (`test_transfers.sh`)

```bash
#!/bin/bash

echo "=== Banking Transfer System Test ==="
echo ""

CREATE_RESPONSE=$(curl -s -X POST http://localhost:3000/create-users)
echo "$CREATE_RESPONSE" | jq '.'

ALICE_ID=$(echo "$CREATE_RESPONSE" | jq -r '.users[0]._id')
BOB_ID=$(echo "$CREATE_RESPONSE" | jq -r '.users[1]._id')

echo ""
echo "Alice ID: $ALICE_ID"
echo "Bob ID: $BOB_ID"
echo ""

curl -s http://localhost:3000/users | jq '.'
echo ""

curl -s -X POST http://localhost:3000/transfer \
  -H "Content-Type: application/json" \
  -d "{
    \"fromUserId\": \"$ALICE_ID\",
    \"toUserId\": \"$BOB_ID\",
    \"amount\": 150
  }" | jq '.'
echo ""

curl -s http://localhost:3000/users | jq '.'
echo ""

curl -s -X POST http://localhost:3000/transfer \
  -H "Content-Type: application/json" \
  -d "{
    \"fromUserId\": \"$BOB_ID\",
    \"toUserId\": \"$ALICE_ID\",
    \"amount\": 900
  }" | jq '.'
echo ""

curl -s http://localhost:3000/users | jq '.'
echo ""

echo "=== Test Complete ==="
```

**Run the script:**
```bash
chmod +x test_transfers.sh
./test_transfers.sh
```

---

## Error Scenarios to Test

1. **Invalid User ID** → 400 Bad Request  
2. **Non-existent User** → 404 Not Found  
3. **Negative Amount** → 400 Bad Request  
4. **Missing Fields** → 400 Bad Request  
5. **Same Account Transfer** → 400 Bad Request  

---

## How It Works (Without Transactions)

1. Validate Input  
2. Fetch Sender Account  
3. Fetch Receiver Account  
4. Check Balance ≥ Amount  
5. Deduct from Sender  
6. Add to Receiver  
7. Return Success  

> Pre-validation + document-level atomicity ensures safe operations, but high-concurrency scenarios may require **MongoDB transactions** or **optimistic locking** for production.

---

## Production Considerations

- MongoDB Transactions for ACID compliance  
- Optimistic Locking  
- Audit Trail  
- Authentication & Rate Limiting  
- Input Sanitization & Error Monitoring  
- Connection Pooling  

**Example with Transactions:**
```javascript
const session = await mongoose.startSession();
session.startTransaction();
try {
  await sender.save({ session });
  await receiver.save({ session });
  await session.commitTransaction();
} catch (error) {
  await session.abortTransaction();
  throw error;
} finally {
  session.endSession();
}
```

---

## Package.json Scripts

```json
"scripts": {
  "start": "node server.js",
  "dev": "nodemon server.js",
  "test": "bash test_transfers.sh"
}
```

---

## Key Learning Points

- **Balance Validation:** Always check before updates  
- **Sequential Operations:** Deduct → Add  
- **Error Handling:** Return proper HTTP codes & messages  
- **Data Consistency:** Document-level atomicity & version tracking  
- **RESTful API Design:** Clear endpoints and meaningful responses

