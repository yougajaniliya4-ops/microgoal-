# 🏗️ System Architecture & Design

## Table of Contents
1. [High-Level Architecture](#high-level-architecture)
2. [System Components](#system-components)
3. [Data Flow](#data-flow)
4. [Authentication Flow](#authentication-flow)
5. [API Architecture](#api-architecture)
6. [Database Design](#database-design)
7. [Frontend Architecture](#frontend-architecture)

---

## High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                      CLIENT TIER                                │
│                   (React + Vite)                                │
│  ┌─────────────────────────────────────────────────────────┐   │
│  │  Dashboard │ AuthForm │ GoalsList │ CreateGoalForm     │   │
│  └─────────────────────────────────────────────────────────┘   │
│           │                                          │           │
│           │    AXIOS HTTP CLIENT (api.js)           │           │
│           └───────────────────────────────────────────┘          │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                    HTTP REST API
                   (Port 5173 → 8000)
                           │
┌──────────────────────────┴──────────────────────────────────────┐
│                    APPLICATION TIER                            │
│                  (Node.js + Express)                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  AuthRouter        GoalRouter        Middlewares         │  │
│  │  ├─ /signup        ├─ /create        ├─ authMiddleware  │  │
│  │  ├─ /login         ├─ /goals/all     ├─ errorMiddleware │  │
│  │  └─ /me            ├─ GET /:id       └─ CORS            │  │
│  │                    ├─ PUT /:id                           │  │
│  │                    └─ DELETE /:id                        │  │
│  └──────────────────────────────────────────────────────────┘  │
│           │                                          │           │
│           │  MONGOOSE ODM (Models & Schemas)        │           │
│           └───────────────────────────────────────────┘          │
└──────────────────────────┬──────────────────────────────────────┘
                           │
                      TCP Connection
                     (Port 27017)
                           │
┌──────────────────────────┴──────────────────────────────────────┐
│                      DATA TIER                                  │
│                  MongoDB Database                              │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Collection: users          Collection: goals            │  │
│  │  ├─ _id                     ├─ _id                       │  │
│  │  ├─ name                    ├─ title                     │  │
│  │  ├─ email (unique)          ├─ completed                │  │
│  │  └─ password (hashed)       ├─ user (FK)                │  │
│  │                             └─ createdAt (optional)     │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
```

---

## System Components

### 1. **Frontend (React + Vite)**

#### Purpose
- User interface for managing goals
- Authentication UI (login/signup)
- Real-time goal updates

#### Key Components
```
App
├── Dashboard (Main Container)
│   ├── Header (User Info + Logout)
│   ├── CreateGoalForm (Input Form)
│   └── GoalsList (Display Goals)
│       └── GoalItem[] (Individual Goals)
│
└── AuthForm (Auth Container - if not logged in)
    ├── Signup Form
    └── Login Form
```

#### Key Files
- **api.js** - Axios configuration with interceptors
  - Base URL: Environment variable
  - Request interceptor: Injects JWT token
  - Response interceptor: Handles 401 errors

- **Components** - React functional components with Hooks
  - useState: Local state management
  - useEffect: Side effects and API calls

---

### 2. **Backend (Node.js + Express)**

#### Purpose
- RESTful API for CRUD operations
- User authentication and authorization
- Data validation and error handling

#### Key Layers

**Router Layer** (Endpoints)
```javascript
router.post("/create")     // Handle requests
router.get("/goals/all")
router.put("/:id")
router.delete("/:id")
```

**Middleware Layer** (Request Processing)
```javascript
authMiddleware          // Verify JWT
errorMiddleware         // Handle errors
express.json()          // Parse JSON
cors()                  // Allow cross-origin
```

**Model Layer** (Database Access)
```javascript
userModel.findOne({email})    // Query operations
goalModel.find({user})
goalModel.findByIdAndUpdate()
```

**Schema Layer** (Data Structure)
```javascript
userSchema.define({      // Define data structure
  name: String,
  email: String,
  password: String
})
```

---

### 3. **Database (MongoDB)**

#### Collections

**users**
```javascript
{
  _id: ObjectId,
  name: String,
  email: String (unique),
  password: String (bcrypt hashed)
}
```

**goals**
```javascript
{
  _id: ObjectId,
  title: String,
  completed: Boolean,
  user: ObjectId (reference to users._id),
  createdAt: Date
}
```

#### Relationships
- One user can have many goals (1:N)
- Goals belong to one specific user
- No goal exists without a user

---

## Data Flow

### 1. Signup/Login Flow

```
┌─────────────────────────────────────────────────────┐
│ 1. User fills signup form and clicks submit         │
│    {name, email, password}                          │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 2. axios.post('/signup', data)                      │
│    axios interceptor adds headers                   │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 3. Express receives request                         │
│    router.post("/signup", async (req, res, next))  │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 4. Validation (Joi schema)                          │
│    - Check required fields                          │
│    - Validate email format                          │
│    - Check password length                          │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 5. Check if user exists                            │
│    userModel.findOne({email})                       │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 6. Hash password with bcryptjs                      │
│    bcrypt.hash(password, 10)                        │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 7. Create user in database                         │
│    new userModel({ name, email, password })        │
│    await user.save()                                │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 8. Generate JWT token                              │
│    jwt.sign({userId}, SECRET_KEY, {expiresIn: "1d"})│
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 9. Send response with token                        │
│    res.json({ success, token, data })              │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 10. Frontend receives token                         │
│     localStorage.setItem('token', response.token)   │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 11. Redirect to Dashboard                          │
│     User can now access protected routes           │
└─────────────────────────────────────────────────────┘
```

---

## Authentication Flow

### Request with JWT Token

```
┌─────────────────────────────────────────────────────┐
│ 1. Frontend makes protected request                │
│    axios.get('/goals/all')                          │
│    Token in localStorage                            │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 2. Axios REQUEST INTERCEPTOR runs                  │
│    - Gets token from localStorage                   │
│    - Adds to Authorization header                   │
│    Authorization: Bearer <token>                    │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 3. Express receives request                         │
│    Router calls authMiddleware first               │
└────────────────────┬────────────────────────────────┘
                     │
┌────────────────────▼────────────────────────────────┐
│ 4. authMiddleware verifies token                   │
│    - Extract token from header                      │
│    - Verify with SECRET_KEY                         │
│    - Decode to get userId                           │
│    - req.user = userId                              │
│    - next() → route handler                         │
└────────────────────┬────────────────────────────────┘
                     │
     ┌───────────────┴────────────────┐
     │                                │
  ✅ VALID                        ❌ INVALID
     │                                │
     ▼                                ▼
Route Handler              Return 401 Unauthorized
Execute query              Axios RESPONSE interceptor
Find goals for user        localStorage.removeItem('token')
Send response              Redirect to login
     │
└────┬─────────────────┐
     │ Response comes back to frontend
     │
     ▼
Axios RESPONSE interceptor
Update state with goals
Display in UI
```

---

## API Architecture

### Request-Response Lifecycle

```javascript
// FRONTEND
axios.post('/create', {title: "Learn React"})
//        │
//        ├─ Interceptor adds Authorization header
//        └─ Sends HTTP POST to http://localhost:8000/create

// BACKEND - Express Router
router.post("/create", authMiddleware, async (req, res, next) => {
  // 1. authMiddleware runs first
  //    - Verifies token
  //    - Sets req.user
  //    - Calls next()
  
  // 2. Route handler executes
  try {
    const { title } = req.body;
    const goal = new goalModel({
      title: title,
      user: req.user  // From authMiddleware
    });
    await goal.save();
    
    // 3. Success response
    res.status(201).json({
      success: true,
      message: "Goal created successfully",
      data: goal
    });
  } catch (err) {
    // 4. Error handling
    next(err);  // Pass to errorMiddleware
  }
});

// BACKEND - Error Middleware
errorMiddleware(err, req, res, next) {
  res.status(err.status || 500).json({
    success: false,
    message: err.message
  });
}

// FRONTEND - Response
axios response (success) → update state → re-render
axios error               → show error message
```

### Status Codes Used

```javascript
200 - OK (successful GET, successful PUT login)
201 - Created (successful POST create)
400 - Bad Request (validation failed, missing fields)
401 - Unauthorized (no token, invalid token)
404 - Not Found (resource doesn't exist)
500 - Server Error (database error, internal error)
```

---

## Database Design

### Schema Relationships

```
┌──────────────┐
│    users     │
├──────────────┤
│ _id (PK)     │◇────────┐ (1)
│ name         │         │
│ email        │         │ 1:N
│ password     │         │
└──────────────┘         │ (Many)
                         │
                    ┌────▼──────────┐
                    │     goals      │
                    ├────────────────┤
                    │ _id (PK)       │
                    │ title          │
                    │ completed      │
                    │ user (FK) ─────┘
                    └────────────────┘
```

### Mongoose Relations

```javascript
// User can have many goals
// In GoalSchema:
user: {
  type: mongoose.Schema.Types.ObjectId,
  ref: "User",
  required: true
}

// Query with population:
Goal.findOne({_id: goalId})
    .populate('user')  // Include full user object
    .exec()
```

---

## Frontend Architecture

### Component Hierarchy & Props

```
App
├── Dashboard (if authenticated)
│   ├── Header
│   │   ├── title: "🎯 MicroGoals"
│   │   ├── user: { name, _id }
│   │   └── onLogout: function
│   │
│   ├── CreateGoalForm
│   │   ├── onGoalCreated: function
│   │   └── State: title, loading, error
│   │
│   └── GoalsList
│       ├── userId: string
│       ├── State: goals[], loading, error
│       ├── Methods: fetchGoals(), onGoalUpdated()
│       │
│       └── GoalItem[] (for each goal)
│           ├── goal: object
│           ├── onUpdate: function
│           ├── onDelete: function
│           └── State: isDeleting
│
└── AuthForm (if not authenticated)
    ├── isSignup: boolean
    ├── onLoginSuccess: function
    ├── State: name, email, password, error, loading
    └── Methods: handleSubmit()
```

### State Management

**Global State (localStorage)**
```javascript
// Token
localStorage.setItem('token', jwtToken)
localStorage.getItem('token')
localStorage.removeItem('token')
```

**Component State (useState)**
```javascript
// Dashboard
const [user, setUser] = useState(null)
const [loading, setLoading] = useState(true)

// GoalsList
const [goals, setGoals] = useState([])
const [error, setError] = useState('')

// AuthForm
const [isSignup, setIsSignup] = useState(false)
const [email, setEmail] = useState('')
```

**Side Effects (useEffect)**
```javascript
// Run when component mounts
useEffect(() => {
  if (token) {
    fetchCurrentUser()
  }
}, [])

// Refetch when goal changes
useEffect(() => {
  fetchGoals()
}, [triggerRefresh])
```

---

## Error Handling

### Error Flow

```
┌─────────────────────────────────────┐
│ Error occurs in route handler       │
│ throw new Error("User not found")   │
└────────────────────┬────────────────┘
                     │
┌────────────────────▼────────────────────────┐
│ Caught by catch block                       │
│ next(err) → passes to errorMiddleware      │
└────────────────────┬────────────────────────┘
                     │
┌────────────────────▼────────────────────────┐
│ errorMiddleware routes error                │
│ Sets status code                            │
│ Sends JSON response with error message      │
└────────────────────┬────────────────────────┘
                     │
┌────────────────────▼────────────────────────┐
│ Axios RESPONSE interceptor detects error    │
│ Rejects promise                             │
└────────────────────┬────────────────────────┘
                     │
┌────────────────────▼────────────────────────┐
│ .catch() in component                       │
│ Sets error state                            │
│ Displays error message to user              │
└─────────────────────────────────────────────┘
```

---

## Security Architecture

### Password Security
```
User's Password
      ↓
bcryptjs (10 salt rounds)
      ↓
Hashed Password (stored in DB)
      ↓
On Login: Compare input with stored hash
```

### Token Security
```
JWT Creation
├─ Payload: { userId }
├─ Secret: SECRET_KEY (from environment)
├─ Algorithm: HS256
└─ Expiration: 24 hours

Token Verification
├─ Extract from Authorization header
├─ Verify signature with SECRET_KEY
├─ Check expiration
└─ Extract userId
```

### Data Protection
```
✅ CORS configured for localhost only
✅ Input validation with Joi
✅ Protected routes with authMiddleware
✅ No sensitive data in logs
✅ Environment variables for secrets
```

---

## Performance Considerations

### Frontend Optimization
- ✅ React components memoization
- ✅ Conditional rendering (don't load until needed)
- ✅ CSS animations (GPU-accelerated)
- ✅ Lazy loading (if needed)

### Backend Optimization
- ✅ Database indexing (email, user_id)
- ✅ Mongoose query optimization
- ✅ Caching headers (for future improvement)
- ✅ Connection pooling (Mongoose default)

### Database Optimization
- ✅ Indexes on frequently queried fields
- ✅ ObjectId references (not full objects)
- ✅ Compound indexes (user + completed)

---

## Scalability Considerations

### Current State (Development)
- Single instance Node.js server
- Local/shared MongoDB instance
- No caching layer
- No session persistence

### Future Improvements
- **Load Balancing**: Multiple Node.js instances behind nginx
- **Caching**: Redis for token blacklist and frequently accessed data
- **Database**: Connection pooling, read replicas
- **Frontend**: CDN for static assets
- **Monitoring**: Error tracking, performance monitoring

---

## Technology Justification

| Technology | Why Chosen |
|------------|-----------|
| **Express.js** | Lightweight, popular, great for RESTful APIs |
| **MongoDB** | NoSQL flexibility, easy to scale, JSON-like documents |
| **Mongoose** | Schema validation, relationships, built-in methods |
| **JWT** | Stateless, secure, industry standard |
| **bcryptjs** | Password hashing standard, prevents rainbow tables |
| **React** | Component-based, reusable, great ecosystem |
| **Vite** | Fast build tool, great dev experience |
| **Axios** | Promise-based, interceptors, error handling |

---

**Last Updated**: March 2026

See [INDEX.md](./INDEX.md) for documentation navigation
