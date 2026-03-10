# MicroGoals Full-Stack Application Setup Guide

## Project Overview

This is a full-stack CRUD application for managing user goals (to-do items) with:
- **Backend**: Node.js + Express.js + MongoDB + Mongoose
- **Frontend**: React + Vite + Axios
- **Authentication**: JWT-based user authentication
- **Database**: MongoDB Atlas or Local MongoDB

---

## Prerequisites

Before getting started, ensure you have installed:
- **Node.js** (v14 or higher) - [Download](https://nodejs.org/)
- **MongoDB** (local or MongoDB Atlas account) - [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
- **Git** (optional, for version control)
- **VS Code** or any code editor

---

## Project Structure

```
MicroProjectApi/
├── Backend/
│   ├── Index.js                 # Main server entry point
│   ├── package.json             # Backend dependencies
│   ├── .env                     # Environment variables
│   ├── .env.example             # Environment template
│   ├── Middlewares/
│   │   ├── authMiddleware.js    # JWT authentication
│   │   └── errormiddleware.js   # Error handling
│   ├── Model/
│   │   ├── UserModel.js         # User database model
│   │   └── GoalModel.js         # Goal database model
│   ├── Router/
│   │   ├── AuthRouter.js        # Auth endpoints (signup/login)
│   │   └── GoalRouter.js        # Goal CRUD endpoints
│   ├── Schema/
│   │   ├── User.js              # User schema definition
│   │   └── GoalSchema.js        # Goal schema definition
│   ├── validation/
│   │   └── userValidation.js    # Input validation
│   └── utills/
│       └── genratetoken.js      # JWT token generation
│
├── Frontend/
│   ├── index.html               # HTML entry point
│   ├── vite.config.js           # Vite configuration
│   ├── package.json             # Frontend dependencies
│   ├── .env                     # Environment variables
│   └── src/
│       ├── main.jsx             # React entry point
│       ├── App.jsx              # Main app component
│       ├── App.css              # Styles
│       └── services/
│           └── api.js           # Axios API client
│
└── SETUP_GUIDE.md               # This file
```

---

## Step 1: MongoDB Setup

### Option A: MongoDB Atlas (Cloud - Recommended)

1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create a free account and log in
3. Create a new cluster:
   - Click "Create" → Select "M0 Free" tier
   - Choose your region and click "Create Cluster"
4. **Create a Database User**:
   - Go to "Database Access"
   - Click "Add New Database User"
   - Create username (e.g., `microgoals_user`)
   - Create password (save it securely)
   - Click "Add User"
5. **Whitelist IP Address**:
   - Go to "Network Access"
   - Click "Add IP Address"
   - Select "Allow Access from Anywhere" (for development) or add your IP
   - Click "Confirm"
6. **Get Connection String**:
   - Click "Databases" → "Connect"
   - Click "Drivers"
   - Copy the connection string (looks like: `mongodb+srv://username:password@cluster.mongodb.net/dbname?retryWrites=true&w=majority`)
   - Replace `<username>`, `<password>`, and database name

### Option B: Local MongoDB

1. Install MongoDB Community Edition:
   - **Windows**: [MongoDB Windows Installer](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/)
   - **Mac**: `brew tap mongodb/brew && brew install mongodb-community`
   - **Linux**: Follow [official docs](https://docs.mongodb.com/manual/administration/install-on-linux/)

2. Start MongoDB:
   - **Windows**: MongoDB runs as a service automatically
   - **Mac/Linux**: `brew services start mongodb-community`

3. Connection string: `mongodb://localhost:27017/microgoals`

---

## Step 2: Backend Setup

### 2.1 Install Dependencies

```bash
cd Backend
npm install
```

### 2.2 Configure Environment Variables

Update **Backend/.env**:

```env
# Server
PORT=8000

# MongoDB Connection
MONGO_URL=mongodb+srv://username:password@cluster.mongodb.net/microgoals?retryWrites=true&w=majority
# OR for local MongoDB:
# MONGO_URL=mongodb://localhost:27017/microgoals

# JWT Secret Key (generate a secure random string)
SECRET_KEY=your_super_secret_jwt_key_change_this_in_production
```

**Generate a secure SECRET_KEY**:
```bash
node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### 2.3 Create .env.example (for team collaboration)

Create **Backend/.env.example**:
```env
PORT=8000
MONGO_URL=mongodb+srv://username:password@cluster.mongodb.net/microgoals
SECRET_KEY=your_super_secret_key_here
```

### 2.4 Start the Backend Server

```bash
npm start
```

You should see:
```
MongoDB Connected
Server running on port 8000
```

Test the API:
- Open browser: `http://localhost:8000`
- Should return: JSON with API endpoints

---

## Step 3: Frontend Setup

### 3.1 Install Dependencies

```bash
cd Frontend
npm install
```

### 3.2 Configure Environment Variables

Update **Frontend/.env**:

```env
VITE_API_URL=http://localhost:8000
```

Create **Frontend/.env.example**:
```env
VITE_API_URL=http://localhost:8000
```

### 3.3 Start the Frontend Server

```bash
npm run dev
```

You should see:
```
  VITE v5.0.0  LOCAL
  ➜  Local:   http://127.0.0.1:5173/
```

---

## Step 4: API Endpoints Reference

### Authentication Endpoints

#### **POST /signup**
Create a new user account

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "user is created",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

#### **POST /login**
Authenticate and get JWT token

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "login successfully",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k1",
    "name": "John Doe",
    "email": "john@example.com"
  }
}
```

### Goal Endpoints (Protected - Require JWT Token)

> All goal endpoints require Authorization header: `Authorization: Bearer <token>`

#### **POST /create**
Create a new goal

**Request Body:**
```json
{
  "title": "Complete project setup"
}
```

**Response:**
```json
{
  "success": true,
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k2",
    "title": "Complete project setup",
    "completed": false,
    "user": "65a1b2c3d4e5f6g7h8i9j0k1"
  }
}
```

#### **GET /:id**
Get a specific goal

**URL:** `GET /65a1b2c3d4e5f6g7h8i9j0k2`

**Response:**
```json
{
  "success": true,
  "data": {
    "_id": "65a1b2c3d4e5f6g7h8i9j0k2",
    "title": "Complete project setup",
    "completed": false,
    "user": "65a1b2c3d4e5f6g7h8i9j0k1"
  }
}
```

#### **PUT /:id**
Update goal (mark as completed)

**URL:** `PUT /65a1b2c3d4e5f6g7h8i9j0k2`

**Request Body:**
```json
{
  "completed": true
}
```

**Response:**
```json
{
  "success": true,
  "message": "the goal is completed"
}
```

#### **DELETE /:id**
Delete a goal

**URL:** `DELETE /65a1b2c3d4e5f6g7h8i9j0k2`

**Response:**
```json
{
  "message": "Goal deleted"
}
```

---

## Step 5: Frontend Usage Examples

### Using Axios to Call APIs

The frontend already has `api.js` configured with:
- Base URL from environment variables
- Automatic token injection in headers
- Error handling

### Example: Sign Up

```javascript
import api from './services/api'

const handleSignup = async (userData) => {
  try {
    const response = await api.post('/signup', {
      name: userData.name,
      email: userData.email,
      password: userData.password
    })
    localStorage.setItem('token', response.data.token)
    console.log('User created:', response.data.data)
  } catch (error) {
    console.error('Signup failed:', error.response.data.message)
  }
}
```

### Example: Create Goal

```javascript
const handleCreateGoal = async (title) => {
  try {
    const response = await api.post('/create', { title })
    console.log('Goal created:', response.data.data)
  } catch (error) {
    console.error('Failed to create goal:', error.response.data.message)
  }
}
```

### Example: Fetch User's Goals

```javascript
import { useEffect, useState } from 'react'
import api from './services/api'

function GoalsList() {
  const [goals, setGoals] = useState([])
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    fetchGoals()
  }, [])

  const fetchGoals = async () => {
    try {
      // Note: You may need to create a GET /goals endpoint in the backend
      // For now, this is a template
      setLoading(false)
    } catch (error) {
      console.error('Failed to fetch goals:', error)
      setLoading(false)
    }
  }

  return (
    <div>
      {/* Render goals here */}
    </div>
  )
}
```

---

## Step 6: Testing the Application

### Test with Postman/Thunder Client

1. **Test Sign Up:**
   - Method: POST
   - URL: `http://localhost:8000/signup`
   - Body (JSON):
     ```json
     {
       "name": "Test User",
       "email": "test@example.com",
       "password": "test123"
     }
     ```

2. **Test Login:**
   - Method: POST
   - URL: `http://localhost:8000/login`
   - Body (JSON):
     ```json
     {
       "email": "test@example.com",
       "password": "test123"
     }
     ```
   - Copy the returned token

3. **Test Create Goal:**
   - Method: POST
   - URL: `http://localhost:8000/create`
   - Headers: `Authorization: Bearer <your_token>`
   - Body (JSON):
     ```json
     {
       "title": "Learn Full Stack Development"
     }
     ```

---

## Step 7: Running Both Servers

### Option A: Two Terminal Windows

**Terminal 1 - Backend:**
```bash
cd Backend
npm start
```

**Terminal 2 - Frontend:**
```bash
cd Frontend
npm run dev
```

### Option B: Concurrently (Both in one command)

Install concurrently:
```bash
npm install -g concurrently
```

From root directory:
```bash
concurrently "cd Backend && npm start" "cd Frontend && npm run dev"
```

---

## Troubleshooting

### Issue: Cannot connect to MongoDB
- **Solution 1**: Check if MongoDB is running
  - Windows: Services should show "MongoDB Server"
  - Mac/Linux: `brew services list`
- **Solution 2**: Verify connection string in `.env`
- **Solution 3**: Check MongoDB Atlas IP whitelist if using cloud

### Issue: CORS errors
- **Solution**: Ensure CORS is properly configured in `Index.js`
- Verify `origin` includes your frontend URL

### Issue: "Invalid token" error
- **Solution**: Ensure `SECRET_KEY` is set in backend `.env`
- Token might have expired (default: 24 hours)

### Issue: Port already in use
- **Solution 1**: Change PORT in `.env` to different port (e.g., 8001)
- **Solution 2**: Kill process using port:
  - Windows: `netstat -ano | findstr :8000`
  - Mac/Linux: `lsof -ti:8000 | xargs kill -9`

---

## Production Deployment

### Backend Deployment (Heroku, Railway, etc.)

1. Create `.env` file with production variables
2. Ensure `Procfile` exists (if using Heroku):
   ```
   web: node Index.js
   ```
3. Deploy using platform's instructions

### Frontend Deployment (Vercel, Netlify, etc.)

1. Build the app:
   ```bash
   npm run build
   ```
2. Deploy the `dist/` folder

---

## Security Best Practices

1. ✅ Store sensitive data in `.env` (never commit)
2. ✅ Use strong SECRET_KEY for JWT
3. ✅ Implement password hashing (already using bcryptjs)
4. ✅ Use HTTPS in production
5. ✅ Validate all user inputs (implementing with Joi)
6. ✅ Implement rate limiting for API endpoints
7. ✅ Use secure cookies for tokens (in production)

---

## Next Steps

1. **Add a GET /goals endpoint** to fetch all user goals
2. **Implement pagination** for goals listing
3. **Add search/filter functionality**
4. **Create React components** for:
   - Login/Signup forms
   - Goals list view
   - Create goal form
   - Edit goal modal
5. **Add error handling** in frontend
6. **Implement loading states** and spinners
7. **Add form validation** on both frontend and backend

---

## Resources

- [Express.js Documentation](https://expressjs.com/)
- [Mongoose Documentation](https://mongoosejs.com/)
- [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
- [React Documentation](https://react.dev/)
- [Vite Documentation](https://vitejs.dev/)
- [Axios Documentation](https://axios-http.com/)
- [JWT Authentication](https://jwt.io/)

---

## Support

For issues or questions:
1. Check the Troubleshooting section above
2. Review error messages in console
3. Check MongoDB connection logs
4. Verify API endpoints with Postman

---

**Last Updated**: March 2026
