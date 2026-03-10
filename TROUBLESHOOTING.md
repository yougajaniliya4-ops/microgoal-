# 🔧 Troubleshooting Guide

## Common Issues and Solutions

---

## 🔴 Backend Issues

### 1. MongoDB Connection Error
```
Error: connect ECONNREFUSED 127.0.0.1:27017
```

**Solutions:**

**A) Using Local MongoDB**
```bash
# Windows - MongoDB should auto-start as service
# Check if running: Services.msc → search "MongoDB"

# Mac - Start manually
brew services start mongodb-community

# Linux
sudo systemctl start mongod
```

**B) Using MongoDB Atlas**
1. Go to [MongoDB Atlas](https://www.mongodb.com/cloud/atlas)
2. Create/login to account
3. Create cluster (M0 Free tier)
4. Get connection string: Databases → Connect → Copy Connection string
5. Update `.env`:
   ```env
   MONGO_URL=mongodb+srv://username:password@cluster-name.mongodb.net/microgoals?retryWrites=true&w=majority
   ```
6. Replace `username`, `password`, `cluster-name`

**C) Verify MongoDB is installed**
```bash
# Check version
mongod --version

# If not installed:
# Windows: https://docs.mongodb.com/manual/tutorial/install-mongodb-on-windows/
# Mac: brew tap mongodb/brew && brew install mongodb-community
# Linux: https://docs.mongodb.com/manual/administration/install-on-linux/
```

---

### 2. Port 8000 Already in Use
```
Error: listen EADDRINUSE :::8000
```

**Solution:**

**Windows:**
```bash
# Find process using port 8000
netstat -ano | findstr :8000

# Kill process (replace PID with actual process ID)
taskkill /PID <PID> /F

# Or change port in .env
PORT=8001
```

**Mac/Linux:**
```bash
# Find and kill process
lsof -ti:8000 | xargs kill -9

# Or change port
PORT=8001
```

---

### 3. SECRET_KEY Not Set / Invalid Token
```
Error: Cannot read property 'sign' of undefined
Error: Invalid token
```

**Solution:**

1. Check `.env` has `SECRET_KEY`:
   ```bash
   cat Backend/.env | grep SECRET_KEY
   ```

2. If missing, add it:
   ```bash
   # Generate secure key
   node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
   ```

3. Copy output and add to `.env`:
   ```env
   SECRET_KEY=<paste_here>
   ```

4. Restart backend: `npm start`

---

### 4. CORS Error
```
Access to XMLHttpRequest blocked by CORS policy
```

**Solution:**

Check `Backend/Index.js` CORS configuration:

```javascript
app.use(cors({
  origin: ['http://localhost:5173', 'http://localhost:3000'],
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'],
  credentials: true,
  allowedHeaders: ['Content-Type', 'Authorization']
}));
```

**If using different port** (e.g., 3000 for frontend):
```javascript
app.use(cors({
  origin: 'http://localhost:3000',  // Update this
  // ... rest of config
}));
```

---

### 5. Validation Error - "enter the data correctly"
```
Error: Validation error: name is required
```

**Sending Request Missing Required Fields:**

✅ **Correct:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123"
}
```

❌ **Wrong:**
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

Check [Backend/validation/userValidation.js](Backend/validation/userValidation.js#L2-L5) for required fields.

---

### 6. Cannot Find Module Errors
```
Cannot find module './Model/GoalModel'
```

**Solution:**

**Check file path** - Node.js is case-sensitive:
- ✅ `./Model/GoalModel.js` (correct)
- ❌ `./model/goalmodel.js` (wrong)

**Verify file exists:**
```bash
# Windows
dir Backend\Model\

# Mac/Linux
ls -la Backend/Model/
```

**Rebuild node_modules:**
```bash
cd Backend
rm -rf node_modules package-lock.json
npm install
```

---

## 🔴 Frontend Issues

### 1. Frontend Cannot Connect to Backend
```
Error: Failed to connect to backend: http://localhost:8000
```

**Solutions:**

**A) Check backend is running:**
```bash
# Should see: "MongoDB Connected" and "Server running on port 8000"
cd Backend && npm start
```

**B) Check environment variable:**
```bash
# Frontend/.env
VITE_API_URL=http://localhost:8000
```

**C) Verify port not changed:**
- Check `Backend/.env` for `PORT=8000`
- If changed to `8001`, update `Frontend/.env` accordingly

---

### 2. Port 5173 Already in Use
```
Error: Port 5173 is in use
```

**Solution:**

**Option A: Kill process**
```bash
# Windows
netstat -ano | findstr :5173
taskkill /PID <PID> /F

# Mac/Linux
lsof -ti:5173 | xargs kill -9
```

**Option B: Use different port**
```bash
npm run dev -- --port 5174
# App runs at http://localhost:5174
```

---

### 3. Token Not Working / 401 Unauthorized
```
Error: Invalid token / Unauthorized request
```

**Solutions:**

**A) Check token exists:**
```javascript
// Browser DevTools Console
localStorage.getItem('token')
// Should show JWT string like: eyJhbGciOiJIUzI1NiIs...
```

**B) Token expired** (default: 24 hours)
- Sign out and sign in again

**C) Check Authorization header format:**
```
✅ Correct: "Authorization: Bearer eyJhbGciOiJIUzI1NiIs..."
❌ Wrong: "Authorization: eyJhbGciOiJIUzI1NiIs..."
```

Checked in [Frontend/src/services/api.js](Frontend/src/services/api.js#L13-L16)

---

### 4. Blank Page / Nothing Renders
```
White screen with no errors
```

**Solutions:**

**A) Check browser console** (F12):
- Look for red error messages
- Check Network tab for failed requests

**B) Verify React is loaded:**
```javascript
// Browser console
console.log(React)  // Should exist
```

**C) Clear cache:**
```bash
# Hard refresh
Ctrl+Shift+R (Windows/Linux)
Cmd+Shift+R (Mac)
```

**D) Check main.jsx:**
```javascript
// Should render to #root element
ReactDOM.createRoot(document.getElementById('root'))
```

---

### 5. Cannot Sign Up / Login
```
Error: User already exists / User not found
```

**Solutions:**

**A) Check email in request:**
```javascript
// Make sure email field is lowercase and matches
const email = "test@example.com"  // ✅
const email = "Test@Example.com"  // MongoDB treats as different
```

**B) Check database:**
```bash
# Using MongoDB Compass or CLI
use microgoals
db.users.find()  # Should show users
```

**C) Verify validation rules:**
```
Password: minimum 3 characters
Email: valid email format
Name: required (for signup)
```

---

### 6. Styles Not Loading
```
Missing CSS / Unstyled components
```

**Solutions:**

**A) Check CSS imports:**
```javascript
// Each component should have CSS import
import './Dashboard.css'  // ✅
// Missing import ❌
```

**B) Check file exists:**
```bash
# Should exist: Frontend/src/components/ComponentName.css
ls Frontend/src/components/
```

**C) Clear Vite cache:**
```bash
# Stop dev server and cleanup
rm -rf Frontend/.vite
npm run dev  # Restart
```

---

## 🔴 Database Issues

### 1. MongoDB Atlas Connection String Error
```
Error: Invalid connection string / Cannot authenticate
```

**Solution:**

**A) Verify connection string format:**
```
❌ Wrong: mongodb+srv://cluster.mongodb.net/
✅ Right: mongodb+srv://username:password@cluster.mongodb.net/microgoals?retryWrites=true&w=majority
```

**B) Check credentials:**
```
- Username: Exactly as created in "Database Users"
- Password: Exactly as created (URL-encode special chars)
- Database: "microgoals" (or your DB name)
```

**C) Check IP whitelist:**
- Go to MongoDB Atlas → Network Access
- Ensure your IP is whitelisted
- For development, you can allow "0.0.0.0/0" (anywhere)

---

### 2. Cannot Write to Database
```
Error: User has no role assigned / Not Authorized
```

**Solution:**

In MongoDB Atlas:
1. Go to Database Access
2. Click user created
3. Ensure "Read and write to any database" is selected
4. Click Update User

---

## 🔴 Git/Deployment Issues

### 1. Accidentally Committed .env File
```
.env file pushed to GitHub with secrets exposed
```

**Solution:**

**Mark as untracked (but keep locally):**
```bash
# Stop tracking .env file
git rm --cached .env

# Verify it's removed from git
git status

# Commit the change
git commit -m "Remove .env from version control"

# Add to .gitignore (should already be there)
echo ".env" >> .gitignore
```

**Regenerate secrets:**
```
1. Change SECRET_KEY in .env
2. Update any exposed API keys/credentials
3. Restart app
```

---

### 2. node_modules in Git
```
Large commit size / Slow clone
```

**Solution:**

``bash
# Ensure .gitignore includes:
cat Backend/.gitignore
# Should contain: node_modules/

# Remove if accidentally committed:
git rm -r --cached node_modules
git commit -m "Remove node_modules from git"
```

---

## 🟡 Performance Issues

### 1. Slow API Responses
```
API takes 5+ seconds to respond
```

**Solutions:**

**A) Check MongoDB connection:**
- Ensure MongoDB is running
- Check network latency to Atlas (if using cloud)

**B) Add indexing** (MongoDB Atlas):
```javascript
// In Schema
email: {
  type: String,
  index: true  // Add this
}
```

**C) Check query efficiency:**
```javascript
// Good: Specific query
goalModel.find({ user: req.user })

// Bad: Find all, then filter
goalModel.find({})
```

---

## 🟢 Tips for Prevention

1. **Use VS Code Thunder Client** for API testing instead of browser
2. **Check logs immediately** when something breaks
3. **Test one change at a time** to identify issues
4. **Use git commits** frequently to track what broke
5. **Monitor MongoDB Atlas** dashboard for errors
6. **Keep secrets secure** - never commit `.env`

---

## 📞 Getting Help

If issue persists:

1. **Check logs:**
   ```bash
   # Backend console output
   npm start
   
   # Browser DevTools (F12)
   # Check Console and Network tabs
   ```

2. **Verify setup:**
   - Is MongoDB running?
   - Is backend running (`npm start`)?
   - Is frontend running (`npm run dev`)?
   - Are environment variables set?

3. **Try resetting:**
   ```bash
   # Backend
   cd Backend
   rm -rf node_modules package-lock.json
   npm install
   npm start
   
   # Frontend
   cd Frontend
   rm -rf node_modules package-lock.json
   npm install
   npm run dev
   ```

4. **Check this guide** for exact error message

---

**Last Updated**: March 2026

For detailed setup, see [SETUP_GUIDE.md](./SETUP_GUIDE.md)
