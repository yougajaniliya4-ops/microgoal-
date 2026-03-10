# 🚀 Quick Reference Guide

## Common Development Tasks

### Starting the Application

**Full Stack (Both Servers)**
```bash
# Terminal 1 - Backend
cd Backend
npm start
# Runs on: http://localhost:8000

# Terminal 2 - Frontend
cd Frontend
npm run dev
# Runs on: http://localhost:5173
```

### Testing API Endpoints

**Using VS Code Thunder Client or Postman**

#### 1. Sign Up
```
POST http://localhost:8000/signup
Content-Type: application/json

{
  "name": "Test User",
  "email": "test@example.com",
  "password": "test123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "User created successfully",
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "data": {
    "_id": "123abc",
    "name": "Test User",
    "email": "test@example.com"
  }
}
```

#### 2. Login
```
POST http://localhost:8000/login
Content-Type: application/json

{
  "email": "test@example.com",
  "password": "test123"
}
```

Copy the returned `token` for authenticated requests.

#### 3. Create Goal
```
POST http://localhost:8000/create
Authorization: Bearer <paste_token_here>
Content-Type: application/json

{
  "title": "Learn full-stack development"
}
```

#### 4. Get All Goals
```
GET http://localhost:8000/goals/all
Authorization: Bearer <paste_token_here>
```

#### 5. Update Goal (Mark as Complete)
```
PUT http://localhost:8000/<goal_id>
Authorization: Bearer <paste_token_here>
Content-Type: application/json

{
  "completed": true
}
```

Replace `<goal_id>` with the goal's MongoDB ID.

#### 6. Delete Goal
```
DELETE http://localhost:8000/<goal_id>
Authorization: Bearer <paste_token_here>
```

---

## Environment Variables Cheat Sheet

### Backend (.env)
```env
# Server port
PORT=8000

# MongoDB connection
MONGO_URL=mongodb://localhost:27017/microgoals
# OR for MongoDB Atlas:
# MONGO_URL=mongodb+srv://user:password@cluster.mongodb.net/microgoals?retryWrites=true&w=majority

# JWT secret key (generate with: node -e "console.log(require('crypto').randomBytes(32).toString('hex'))")
SECRET_KEY=your_random_secret_key_here
```

### Frontend (.env)
```env
# Backend API URL
VITE_API_URL=http://localhost:8000
```

---

## File Structure Reference

### Adding a New Schema

**1. Create Schema** - `Backend/Schema/NewSchema.js`
```javascript
const mongoose = require("mongoose");
const Schema = mongoose.Schema;

const newSchema = new Schema({
  title: { type: String, required: true },
  description: String,
  createdAt: { type: Date, default: Date.now }
});

module.exports = newSchema;
```

**2. Create Model** - `Backend/Model/NewModel.js`
```javascript
const mongoose = require("mongoose");
const schema = require("../Schema/NewSchema");
const model = new mongoose.model("CollectionName", schema);
module.exports = model;
```

**3. Create Routes** - `Backend/Router/NewRouter.js`
```javascript
const express = require("express");
const router = express.Router();
const model = require("../Model/NewModel");
const authMiddleware = require("../Middlewares/authMiddleware");

router.post("/create", authMiddleware, async (req, res, next) => {
  try {
    const newDoc = new model(req.body);
    await newDoc.save();
    res.status(201).json({ success: true, data: newDoc });
  } catch (err) {
    next(err);
  }
});

module.exports = router;
```

**4. Import in Index.js**
```javascript
const newRouter = require("./Router/NewRouter");
app.use("/", newRouter);
```

---

## Common Code Snippets

### Frontend - Call API with Error Handling
```javascript
import { useState } from 'react'
import api from './services/api'

function MyComponent() {
  const [data, setData] = useState(null)
  const [loading, setLoading] = useState(false)
  const [error, setError] = useState(null)

  const fetchData = async () => {
    setLoading(true)
    setError(null)
    try {
      const response = await api.get('/endpoint')
      setData(response.data.data)
    } catch (err) {
      setError(err.response?.data?.message || 'An error occurred')
    } finally {
      setLoading(false)
    }
  }

  return (
    <div>
      {loading && <p>Loading...</p>}
      {error && <p style={{ color: 'red' }}>{error}</p>}
      {data && <p>{JSON.stringify(data)}</p>}
      <button onClick={fetchData}>Fetch Data</button>
    </div>
  )
}
```

### Backend - Protected Route with Validation
```javascript
const express = require("express");
const router = express.Router();
const authMiddleware = require("../Middlewares/authMiddleware");

router.post("/create", authMiddleware, async (req, res, next) => {
  try {
    // Validate input
    const { title } = req.body;
    if (!title) {
      const err = new Error("Title is required");
      err.status = 400;
      return next(err);
    }

    // Get authenticated user
    const userId = req.user;

    // Create document
    const document = new Model({ title, user: userId });
    await document.save();

    res.status(201).json({
      success: true,
      message: "Created successfully",
      data: document
    });
  } catch (error) {
    next(error);
  }
});

module.exports = router;
```

---

## Debugging Tips

### Check Backend Logs
```bash
# Terminal shows real-time logs
npm start
# Look for:
# - "MongoDB Connected"
# - "Server running on port 8000"
# - Error messages
```

### Check Token in Browser
```javascript
// Open browser DevTools Console
localStorage.getItem('token')
// Should show JWT token string
```

### Test API with curl
```bash
# Sign up
curl -X POST http://localhost:8000/signup \
  -H "Content-Type: application/json" \
  -d '{"name":"Test","email":"test@example.com","password":"test123"}'

# Login
curl -X POST http://localhost:8000/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"test123"}'

# Protected endpoint with token
curl -X GET http://localhost:8000/goals/all \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### Verify MongoDB Connection
```bash
# Check if MongoDB is running
# Windows: Check Services
# Mac/Linux:
brew services list | grep mongodb
```

---

## Performance Tips

1. **Use indexing** in MongoDB for frequently queried fields
2. **Implement pagination** for large data sets
3. **Cache responses** using Redis for read-heavy operations
4. **Use async/await** for non-blocking operations
5. **Implement rate limiting** to prevent abuse

---

## Security Checklist

- ✅ Never commit `.env` file
- ✅ Use strong SECRET_KEY (generate: `node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"`)
- ✅ Validate all user inputs on backend
- ✅ Hash passwords with bcrypt
- ✅ Use HTTPS in production
- ✅ Implement CORS properly
- ✅ Use JWT with expiration
- ✅ Never expose sensitive data in responses

---

## Useful Commands

```bash
# Install new package
npm install package-name

# Install dev dependency
npm install --save-dev package-name

# Remove node_modules and reinstall
rm -rf node_modules && npm install

# Kill process on port
# Windows:
netstat -ano | findstr :8000
taskkill /PID <PID> /F

# Mac/Linux:
lsof -ti:8000 | xargs kill -9

# Generate JWT secret
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

---

## More Resources

- [Express.js Guide](https://expressjs.com/)
- [Mongoose ODM](https://mongoosejs.com/)
- [React Docs](https://react.dev/)
- [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
- [JWT.io](https://jwt.io/)
- [REST API Best Practices](https://restfulapi.net/)

---

**Last Updated**: March 2026
