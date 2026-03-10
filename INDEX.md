# 📚 Documentation Index

## Quick Navigation

All documentation for the MicroGoals application is organized here. Choose the guide based on what you need:

---

## 🚀 Getting Started (Start Here!)

**→ [README.md](./README.md)** - Project overview and quick start  
**→ [SETUP_GUIDE.md](./SETUP_GUIDE.md)** - Complete setup instructions for both backend and frontend

**Time needed:** 15-30 minutes

---

## 📖 Learning & Development

**→ [QUICK_REFERENCE.md](./QUICK_REFERENCE.md)** - Common tasks, code snippets, and API examples  
**→ [ARCHITECTURE.md](./ARCHITECTURE.md)** - System design and data flow explanation

**Best for:** Understanding how the app works and developing new features

---

## 🔧 Troubleshooting & Help

**→ [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)** - Detailed solutions for common problems

**When to use:** Something isn't working or you're getting an error

---

## 📋 File-by-File Guide

### Backend Structure

```
Backend/
├── Index.js                          # Main server file
│                                     # - Initialize Express app
│                                     # - Connect to MongoDB
│                                     # - Configure middleware
│                                     # - Register routes
│
├── Middlewares/
│   ├── authMiddleware.js             # JWT authentication
│   │                                 # - Verify JWT tokens
│   │                                 # - Extract user ID
│   │                                 # - Protect routes
│   └── errormiddleware.js            # Error handler
│                                     # - Catch errors
│                                     # - Format error responses
│
├── Model/
│   ├── UserModel.js                  # User database model
│   │                                 # - Queries users
│   │                                 # - References UserSchema
│   └── GoalModel.js                  # Goal database model
│                                     # - Queries goals
│                                     # - References GoalSchema
│
├── Schema/
│   ├── User.js                       # User schema definition
│   │                                 # - name (required)
│   │                                 # - email (unique)
│   │                                 # - password (hashed)
│   └── GoalSchema.js                 # Goal schema definition
│                                     # - title (required)
│                                     # - completed (boolean)
│                                     # - user (reference to User)
│
├── Router/
│   ├── AuthRouter.js                 # Authentication endpoints
│   │                                 # - POST /signup
│   │                                 # - POST /login
│   │                                 # - GET /me
│   └── GoalRouter.js                 # Goal CRUD endpoints
│                                     # - POST /create
│                                     # - GET /goals/all
│                                     # - GET /:id
│                                     # - PUT /:id
│                                     # - DELETE /:id
│
├── validation/
│   └── userValidation.js             # Input validation rules
│                                     # - Validates signup/login input
│                                     # - Uses Joi schema
│
├── utills/
│   └── genratetoken.js               # JWT token generation
│                                     # - Creates signed JWT
│                                     # - 24-hour expiration
│
├── package.json                      # Dependencies
├── .env                              # Environment variables (private)
└── .env.example                      # Environment template (public)
```

### Frontend Structure

```
Frontend/
├── index.html                        # HTML entry point
│                                     # - Loads React app
│                                     # - Mounts to #root
│
├── src/
│   ├── main.jsx                      # JavaScript entry
│   │                                 # - Renders App component
│   │                                 # - Mounts to DOM
│   │
│   ├── App.jsx                       # Main component
│   │                                 # - Routes to Dashboard
│   │
│   ├── App.css                       # Global styles
│   │                                 # - Background gradient
│   │                                 # - Animations
│   │
│   ├── services/
│   │   └── api.js                    # Axios configuration
│   │                                 # - Base URL setup
│   │                                 # - Token injection
│   │                                 # - Error handling
│   │
│   └── components/
│       ├── Dashboard.jsx             # Main page
│       │                             # - User header
│       │                             # - Goal creation
│       │                             # - Goals list
│       │
│       ├── AuthForm.jsx              # Login/Signup
│       │                             # - Signup form
│       │                             # - Login form
│       │                             # - Toggle between modes
│       │
│       ├── GoalsList.jsx             # Display all goals
│       │                             # - Fetch from API
│       │                             # - Split pending/completed
│       │                             # - Pass to GoalItem
│       │
│       ├── GoalItem.jsx              # Individual goal
│       │                             # - Toggle completion
│       │                             # - Delete goal
│       │
│       ├── CreateGoalForm.jsx        # Add new goal
│       │                             # - Input form
│       │                             # - POST to API
│       │
│       └── *.css                     # Component styles
│
├── vite.config.js                    # Vite configuration
├── package.json                      # Dependencies
├── .env                              # Environment variables
└── .env.example                      # Environment template
```

---

## 🔄 Data Flow Diagram

### User Registration/Login Flow
```
Frontend (Login Form)
    ↓
    POST /login { email, password }
    ↓
Backend (AuthRouter)
    ↓
    Database Query (Find User)
    ↓
    Password Verification (bcrypt)
    ↓
    JWT Token Generation
    ↓
Response: { token, user }
    ↓
Frontend (localStorage.setItem token)
    ↓
Set as Authorized User
```

### Goal Creation Flow
```
Frontend (CreateGoalForm)
    ↓
    POST /create { title }
    ↓ (with Authorization header + token)
    ↓
Backend (authMiddleware)
    ↓
    Verify JWT Token
    ↓
Backend (GoalRouter)
    ↓
    Create goal with user ID
    ↓
Database (Save to MongoDB)
    ↓
Response: { success, data }
    ↓
Frontend (Update goals list)
    ↓
Show new goal in list
```

---

## 🎓 Learning Path

### Beginner
1. Read [README.md](./README.md) - Understand what the app does
2. Follow [SETUP_GUIDE.md](./SETUP_GUIDE.md) - Get it running locally
3. Test APIs with Postman/Thunder Client
4. Make requests from browser console

### Intermediate
1. Read [QUICK_REFERENCE.md](./QUICK_REFERENCE.md) - Code snippets
2. Explore backend routes in `Backend/Router/`
3. Explore frontend components in `Frontend/src/components/`
4. Modify components and see changes
5. Add new fields to Goal schema

### Advanced
1. Read [ARCHITECTURE.md](./ARCHITECTURE.md) - Understand design
2. Implement new features:
   - Add goal categories
   - Add priority levels
   - Implement search
3. Deploy to production
4. Implement pagination
5. Add caching with Redis

---

## 🌟 Key Concepts Explained

### JWT Authentication
- User logs in with email/password
- Server returns JWT token (signed with SECRET_KEY)
- Frontend stores token in localStorage
- Axios interceptor adds token to each request
- Backend middleware verifies token

**Where:** `Backend/Middlewares/authMiddleware.js`, `Frontend/src/services/api.js`

---

### Express Routes
- Routes handle HTTP requests (GET, POST, PUT, DELETE)
- Middleware runs before route handler
- Handlers access request data and send responses
- Error handlers catch and format errors

**Where:** `Backend/Router/AuthRouter.js`, `Backend/Router/GoalRouter.js`

---

### Database Operations
- Mongoose models define what data looks like
- Models have methods to query database
- Middleware ensures only your data is accessed
- Indexes speed up queries

**Where:** `Backend/Model/`, `Backend/Schema/`

---

### React Components
- Functional components with Hooks
- useState for state management
- useEffect for side effects (API calls)
- Props for passing data to child components

**Where:** `Frontend/src/components/`

---

## 📝 Common Tasks

### Add a New Field to Goals
See: [QUICK_REFERENCE.md - Adding a New Schema](./QUICK_REFERENCE.md#adding-a-new-schema)

### Deploy to Production
See: [SETUP_GUIDE.md - Production Deployment](./SETUP_GUIDE.md#production-deployment)

### Fix MongoDB Connection
See: [TROUBLESHOOTING.md - MongoDB Connection Error](./TROUBLESHOOTING.md#1-mongodb-connection-error)

### Add API Rate Limiting
See: [QUICK_REFERENCE.md - Performance Tips](./QUICK_REFERENCE.md#performance-tips)

---

## 📞 Support Resources

- **MongoDB Docs:** https://docs.mongodb.com/
- **Express Guide:** https://expressjs.com/
- **React Hooks:** https://react.dev/reference/react/hooks
- **Vite Guide:** https://vitejs.dev/
- **JWT Intro:** https://jwt.io/introduction
- **REST API Best Practices:** https://restfulapi.net/

---

## 🗂️ Documentation Files Summary

| File | Purpose | Read Time |
|------|---------|-----------|
| [README.md](./README.md) | Overview and quick start | 5 min |
| [SETUP_GUIDE.md](./SETUP_GUIDE.md) | Complete setup instructions | 20 min |
| [QUICK_REFERENCE.md](./QUICK_REFERENCE.md) | Common tasks and snippets | 10 min |
| [ARCHITECTURE.md](./ARCHITECTURE.md) | System design explanation | 15 min |
| [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) | Problem solutions | 15 min |
| [INDEX.md](./INDEX.md) | This file | 5 min |

---

## ✅ Checklist Before Deployment

- [ ] Regenerate SECRET_KEY
- [ ] Update MONGO_URL to production database
- [ ] Set NODE_ENV=production
- [ ] Update CORS origin for production domain
- [ ] Enable HTTPS
- [ ] Set up environment variables on hosting platform
- [ ] Test all API endpoints
- [ ] Test login/logout flow
- [ ] Test CRUD operations

---

**Total Documentation:** 6 guides  
**Last Updated:** March 2026

---

**Next:** Start with [README.md](./README.md) or if already set up, check [QUICK_REFERENCE.md](./QUICK_REFERENCE.md)
