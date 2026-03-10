# 🚀 Getting Started Guide

## For Complete Beginners - Step by Step

This guide walks you through everything needed to get the application running.

---

## ⏱️ Time Estimate: 20-30 minutes

---

## 📋 Prerequisites Checklist

Before you start, check you have:

- [ ] **Node.js** installed (v14 or higher)
  - Download: https://nodejs.org/
  - Check: Open terminal, type `node --version`
  
- [ ] **MongoDB** installed or account
  - Option 1: [Local MongoDB](https://www.mongodb.com/try/download/community)
  - Option 2: [MongoDB Atlas (Cloud)](https://www.mongodb.com/cloud/atlas)
  
- [ ] **Code Editor** (VS Code recommended)
  - Download: https://code.visualstudio.com/

- [ ] **Git** (optional, for version control)
  - Download: https://git-scm.com/

✅ **Have all of these?** → Continue to Next Steps

---

## 🎯 Step-by-Step Setup

### STEP 1: Set Up Environment Variables (2 minutes)

#### For Backend

1. Open file: `Backend/.env`

2. Add these values:
```env
PORT=8000
MONGO_URL=mongodb://localhost:27017/microgoals
SECRET_KEY=your_secret_key_here
```

**If using MongoDB Atlas instead of local:**
```env
PORT=8000
MONGO_URL=mongodb+srv://username:password@cluster.mongodb.net/microgoals?retryWrites=true&w=majority
SECRET_KEY=your_secret_key_here
```

Replace `username` and `password` with your MongoDB Atlas credentials.

#### For Frontend

1. Open file: `Frontend/.env`

2. File should contain:
```env
VITE_API_URL=http://localhost:8000
```

If this file is empty, add the line above.

---

### STEP 2: Install Backend Dependencies (3 minutes)

1. Open terminal (or PowerShell on Windows)

2. Navigate to Backend folder:
```bash
cd Backend
```

3. Install dependencies:
```bash
npm install
```

You should see many packages being installed. When complete, you'll see:
```
added XXX packages
```

---

### STEP 3: Install Frontend Dependencies (3 minutes)

1. Open **new terminal window** (do NOT use the same terminal)

2. Navigate to Frontend folder:
```bash
cd Frontend
```

3. Install dependencies:
```bash
npm install
```

Again, wait for completion message.

---

### STEP 4: Start MongoDB (1 minute)

**Option A: Local MongoDB**

For Windows:
- MongoDB should start automatically as a service
- Keep it running (you don't need to do anything)

For Mac:
```bash
brew services start mongodb-community
```

For Linux:
```bash
sudo systemctl start mongod
```

**Option B: MongoDB Atlas**
- No action needed, it's cloud-based
- Just ensure your IP is whitelisted

---

### STEP 5: Start Backend Server (2 minutes)

1. In your **first terminal** (where you ran `cd Backend`)

2. Start the server:
```bash
npm start
```

3. Wait for these messages:
```
MongoDB Connected
Server running on port 8000
```

✅ **Backend is running!** Keep this terminal open.

---

### STEP 6: Start Frontend Server (2 minutes)

1. In your **second terminal** (where you ran `cd Frontend`)

2. Start the development server:
```bash
npm run dev
```

3. Wait for this message:
```
Local:   http://127.0.0.1:5173/
```

✅ **Frontend is running!**

---

### STEP 7: Open in Browser (1 minute)

1. Open your web browser (Chrome, Firefox, Safari, Edge)

2. Go to: `http://localhost:5173`

3. You should see:
```
🎯 MicroGoals
Create Account / Login
```

✅ **Success! The app is running!**

---

## 👤 Test the App

### Create an Account

1. Click "Sign up here"

2. Fill in:
   - **Name**: Test User
   - **Email**: test@example.com
   - **Password**: test123

3. Click "Sign Up"

4. You should see a success message and redirect to dashboard

### Create a Goal

1. In the dashboard, type in the input box:
   ```
   Learn Full-Stack Development
   ```

2. Click "Add Goal"

3. Goal appears in the list below

### Mark as Complete

1. Click the checkbox next to your goal

2. Goal moves to "Completed Goals" section

### Delete a Goal

1. Click the trash icon (🗑️) next to a goal

2. Confirm deletion

3. Goal is removed

### Logout

1. Click "Logout" button

2. You return to login page

---

## ✅ Troubleshooting

### Problem: Frontend shows blank page

**Solution:**
1. Open browser Developer Tools (F12)
2. Check Console tab for errors
3. Check if backend is running
4. Refresh page (Ctrl+R or Cmd+R)

### Problem: Cannot connect to backend

**Solution:**
1. Check Backend terminal shows "MongoDB Connected"
2. Verify `.env` files are configured correctly
3. Check port 8000 is not blocked
4. Restart both servers

### Problem: Cannot sign up / Sign up fails

**Solution:**
1. Check all fields filled
2. Check password is at least 3 characters
3. Check email is valid format
4. Check MongoDB is running
5. Check backend console for error messages

### Problem: Cannot see any goals

**Solution:**
1. Are you logged in? (Check header shows username)
2. Try creating a new goal
3. Check backend console for errors
4. Refresh page

### More issues?

Check [TROUBLESHOOTING.md](./TROUBLESHOOTING.md) for detailed solutions.

---

## 📚 Next Steps

**Now that it's running:**

### Learn More
- Read [README.md](./README.md) - Understand the project
- Read [QUICK_REFERENCE.md](./QUICK_REFERENCE.md) - See code examples  
- Read [ARCHITECTURE.md](./ARCHITECTURE.md) - Learn how it works

### Test with Postman
- Download [Postman](https://www.postman.com/downloads/)
- Test API endpoints directly
- See [QUICK_REFERENCE.md](./QUICK_REFERENCE.md#testing-api-endpoints) for examples

### Make Changes
- Open `Backend/` files to understand server code
- Open `Frontend/src/components/` to understand UI code
- Change styles in `.css` files
- Changes appear automatically (hot reload)

### Deploy
- See [SETUP_GUIDE.md](./SETUP_GUIDE.md#step-7-running-both-servers) for deployment instructions

---

## 🎓 What You Learned

You've successfully set up:

✅ Node.js backend with Express  
✅ React frontend with Vite  
✅ MongoDB database  
✅ JWT authentication  
✅ REST API  
✅ Full-stack application  

**You're now a full-stack developer!** 🎉

---

## 📝 Common Commands

```bash
# Start backend
cd Backend && npm start

# Start frontend
cd Frontend && npm start

# Install new package
npm install package-name

# Stop server
Ctrl+C (in terminal)

# Check if port is in use
# Windows:
netstat -ano | findstr :8000

# Mac/Linux:
lsof -ti:8000
```

---

## 💡 Tips

1. **Keep both terminals open** - One for backend, one for frontend
2. **Check error messages** - They tell you what's wrong
3. **Refresh browser** - If something looks wrong
4. **Check MongoDB is running** - Many errors are because of this
5. **Use browser DevTools** (F12) - Check for errors and network requests

---

## 🆘 Need Help?

1. **Quick questions?** Check [QUICK_REFERENCE.md](./QUICK_REFERENCE.md)

2. **Getting an error?** Check [TROUBLESHOOTING.md](./TROUBLESHOOTING.md)

3. **Want to understand?** Check [ARCHITECTURE.md](./ARCHITECTURE.md)

4. **Lost?** Check [INDEX.md](./INDEX.md) for all documentation

---

## ✨ Congratulations!

You now have a **fully functional full-stack application** running locally. 

- Backend API serving on `http://localhost:8000`
- Frontend UI on `http://localhost:5173`
- Database storing your data

**Next:** Explore the code and make it your own! 🚀

---

**Created:** March 2026  
**For:** Full-Stack Development Learning
