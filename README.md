# 🎯 MicroGoals - Full-Stack CRUD Application

A complete full-stack task management application built with **Node.js**, **Express**, **MongoDB**, and **React**.

## 🎬 Quick Start (2 Minutes)

### Prerequisites
- Node.js v14+ installed
- MongoDB (local or [MongoDB Atlas](https://www.mongodb.com/cloud/atlas))
- VS Code or any code editor

### Step 1: Configure Environment Variables

**Backend (.env)**
```env
PORT=8000
MONGO_URL=mongodb://localhost:27017/microgoals
SECRET_KEY=your_super_secret_jwt_key
```

**Frontend (.env)**
```env
VITE_API_URL=http://localhost:8000
```

[📋 Detailed Setup Guide](./SETUP_GUIDE.md)

### Step 2: Install Dependencies

```bash
# Backend
cd Backend
npm install

# Frontend
cd ../Frontend
npm install
```

### Step 3: Run Both Servers

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

**The app is now running at:** `http://localhost:5173`

---

## 📁 Project Structure

```
MicroProjectApi/
├── Backend/
│   ├── Index.js                    # Server entry point
│   ├── package.json
│   ├── .env                        # Environment variables
│   ├── Middlewares/
│   │   ├── authMiddleware.js       # JWT authentication
│   │   └── errormiddleware.js      # Error handling
│   ├── Model/                      # Mongoose models
│   │   ├── UserModel.js
│   │   └── GoalModel.js
│   ├── Router/                     # API routes
│   │   ├── AuthRouter.js
│   │   └── GoalRouter.js
│   ├── Schema/                     # Database schemas
│   │   ├── User.js
│   │   └── GoalSchema.js
│   ├── validation/                 # Input validation
│   │   └── userValidation.js
│   └── utills/                     # Utility functions
│       └── genratetoken.js         # JWT token generation
│
├── Frontend/
│   ├── package.json
│   ├── vite.config.js
│   ├── .env
│   ├── index.html
│   └── src/
│       ├── App.jsx                 # Root component
│       ├── App.css
│       ├── main.jsx                # Entry point
│       ├── services/
│       │   └── api.js              # Axios configuration
│       └── components/
│           ├── Dashboard.jsx       # Main dashboard
│           ├── AuthForm.jsx        # Login/Signup
│           ├── GoalsList.jsx       # Goals display
│           ├── GoalItem.jsx        # Individual goal
│           ├── CreateGoalForm.jsx  # Create goal form
│           └── *.css               # Component styles
│
├── SETUP_GUIDE.md                  # Full setup documentation
└── README.md                       # This file
```

---

## 🔑 Key Features

✅ **User Authentication** - JWT-based signup and login  
✅ **CRUD Operations** - Create, read, update, and delete goals  
✅ **Secure API** - Protected endpoints with authentication middleware  
✅ **MongoDB Integration** - Data persistence with Mongoose  
✅ **Beautiful UI** - Modern React components with responsive CSS  
✅ **Error Handling** - Comprehensive error messaging  
✅ **Input Validation** - Backend validation with Joi  
✅ **Token Storage** - Automatic token injection in requests  

---

## 🛠️ Technology Stack

### Backend
- **Node.js** - JavaScript runtime
- **Express.js** - Web framework
- **MongoDB** - NoSQL database
- **Mongoose** - ODM (Object Document Mapper)
- **JWT** - JSON Web Tokens for authentication
- **Bcrypt.js** - Password hashing
- **Joi** - Data validation
- **CORS** - Cross-origin resource sharing
- **Dotenv** - Environment variable management

### Frontend
- **React** - UI library
- **Vite** - Build tool
- **Axios** - HTTP client
- **CSS3** - Styling

---

## 📚 API Endpoints

### Authentication Endpoints

#### Sign Up
```http
POST /signup
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
```

#### Login
```http
POST /login
Content-Type: application/json

{
  "email": "john@example.com",
  "password": "password123"
}
```

#### Get Current User
```http
GET /me
Authorization: Bearer <token>
```

### Goal Endpoints (Protected - Requires Token)

#### Create Goal
```http
POST /create
Authorization: Bearer <token>
Content-Type: application/json

{
  "title": "Complete project setup"
}
```

#### Get All Goals
```http
GET /goals/all
Authorization: Bearer <token>
```

#### Get Single Goal
```http
GET /:goalId
Authorization: Bearer <token>
```

#### Update Goal
```http
PUT /:goalId
Authorization: Bearer <token>
Content-Type: application/json

{
  "completed": true
}
```

#### Delete Goal
```http
DELETE /:goalId
Authorization: Bearer <token>
```

---

## 🔐 Authentication Flow

1. **User registers** via `/signup` endpoint
2. **JWT token is generated** and returned
3. **Token is stored** in browser's localStorage
4. **Token is automatically injected** in all API requests via Axios interceptor
5. **Backend validates** token in `authMiddleware`
6. **Token expires** after 24 hours (configurable)

---

## 🚀 Development Workflow

### Start Development
```bash
# Backend server
cd Backend && npm start

# Frontend dev server (separate terminal)
cd Frontend && npm run dev
```

### Build for Production
```bash
# Frontend build
cd Frontend && npm run build

# Output: dist/ folder ready for deployment
```

---

## 🐛 Troubleshooting

### MongoDB Connection Failed
```
Solution: Check MONGO_URL in Backend/.env
- Local: mongodb://localhost:27017/microgoals
- Atlas: mongodb+srv://user:pass@cluster.mongodb.net/dbname
```

### CORS Errors
```
Solution: Already configured in Index.js for localhost:5173
- Verify frontend URL matches in CORS config
```

### Token Invalid
```
Solution: Ensure SECRET_KEY is set and consistent
- Generate new: node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
```

### Port Already in Use
```
Solution: Change PORT in .env or kill process
Windows: netstat -ano | findstr :8000
```

---

## 📖 Usage Examples

### React Hook Example - Fetch Goals
```javascript
import { useState, useEffect } from 'react'
import api from './services/api'

function GoalsList() {
  const [goals, setGoals] = useState([])

  useEffect(() => {
    api.get('/goals/all')
      .then(res => setGoals(res.data.data))
      .catch(err => console.error(err))
  }, [])

  return (
    <ul>
      {goals.map(goal => (
        <li key={goal._id}>{goal.title}</li>
      ))}
    </ul>
  )
}
```

### Creating a Goal
```javascript
const createGoal = async (title) => {
  try {
    const response = await api.post('/create', { title })
    console.log('Goal created:', response.data.data)
  } catch (error) {
    console.error('Error:', error.response.data.message)
  }
}
```

---

## 🔒 Security Features

- ✅ Password hashing with bcryptjs
- ✅ JWT-based stateless authentication
- ✅ Protected API endpoints with middleware
- ✅ Input validation with Joi schema
- ✅ Error handling without exposing system details
- ✅ CORS protection
- ✅ Environment variables for sensitive data

---

## 📦 Dependencies

**Backend:**
```json
{
  "bcryptjs": "^3.0.3",
  "cors": "^2.8.6",
  "dotenv": "^17.3.1",
  "express": "^5.2.1",
  "joi": "^18.0.2",
  "jsonwebtoken": "^9.0.3",
  "mongoose": "^9.2.1"
}
```

**Frontend:**
```json
{
  "react": "^18.2.0",
  "react-dom": "^18.2.0",
  "axios": "^1.6.0"
}
```

---

## 🌐 Deployment

### Backend Deployment (Railway, Heroku, etc.)
1. Push code to GitHub
2. Connect repository to hosting platform
3. Set environment variables (MONGO_URL, SECRET_KEY)
4. Deploy

### Frontend Deployment (Vercel, Netlify, etc.)
1. Run `npm run build`
2. Deploy `dist/` folder
3. Update `VITE_API_URL` to production API URL

---

## 🤝 Contributing

1. Create a feature branch
2. Make your changes
3. Test thoroughly
4. Submit a pull request

---

## 📝 License

MIT License - feel free to use this project for learning and development.

---

## 📞 Support

For detailed setup instructions, see [SETUP_GUIDE.md](./SETUP_GUIDE.md)

---

## 🎓 Learning Resources

- [Express.js Documentation](https://expressjs.com/)
- [Mongoose ODM](https://mongoosejs.com/)
- [React Hooks](https://react.dev/reference/react)
- [Vite Guide](https://vitejs.dev/)
- [JWT.io](https://jwt.io/)
- [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)

---

**Made with ❤️ for learning full-stack development**

Last Updated: March 2026
