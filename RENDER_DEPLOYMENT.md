# WhiteBoard Backend - Render Deployment Guide

## Summary of Changes Made

### 1. ✅ Fixed Security Issues
- ✅ Moved hardcoded SECRET_KEY to environment variables (server.js, userController.js, authMiddleware.js)
- ✅ Updated all files to use `process.env.SECRET_KEY`

### 2. ✅ Updated package.json
- ✅ Added name, version, description, main, and engines fields
- ✅ Added Node.js 18.x version requirement

### 3. ✅ Enhanced Socket.io Configuration
- ✅ Added transports: ['websocket', 'polling'] for better compatibility
- ✅ Added credentials: true for CORS

### 4. ✅ Created render.yaml
- ✅ Configured all necessary environment variables

## Step-by-Step Deployment Instructions

### Step 1: Commit Changes Locally
```bash
cd c:\GNIDOC\TCEJORP\WhiteBoard\Backend
git add .
git commit -m "Prepare backend for Render deployment - fix security and configuration"
git push origin main
```

### Step 2: Set Up MongoDB on MongoDB Atlas
1. Go to https://www.mongodb.com/cloud/atlas
2. Create a free account or log in
3. Create a new project named "WhiteBoard"
4. Create a Free cluster (M0)
5. Create database user credentials (e.g., username: admin, password: secure-password)
6. Whitelist your IP or use 0.0.0.0/0 (for public access)
7. Click "Connect" and copy the connection string
8. Replace `<username>` and `<password>` in the URI
9. Save this URI - you'll need it for Render

**Example MongoDB URI:**
```
mongodb+srv://admin:password@cluster.mongodb.net/whiteboard?retryWrites=true&w=majority
```

### Step 3: Deploy on Render
1. Go to https://render.com
2. Sign up with GitHub
3. Click "New +" → "Web Service"
4. Select "Connect a repository" → Choose "WhiteBoard-Backend" repo
5. Fill in the configuration:
   - **Name**: `whiteboard-backend`
   - **Environment**: `Node`
   - **Build Command**: `npm install`
   - **Start Command**: `npm start`
   - **Plan**: Free (or Paid if you want better performance)
6. Click "Advanced" and add Environment Variables:

### Step 4: Add Environment Variables in Render Dashboard
Add these variables in the dashboard:

| Key | Value |
|-----|-------|
| `PORT` | `5000` |
| `NODE_ENV` | `production` |
| `MONGO_URL` | `mongodb+srv://admin:password@cluster.mongodb.net/whiteboard?retryWrites=true&w=majority` |
| `SECRET_KEY` | Generate a secure random key (e.g., use https://randomkeygen.com/) |
| `CLIENT_URL` | Your frontend URL (e.g., `https://whiteboard-frontend.vercel.app`) |

### Step 5: Deploy
1. Click "Create Web Service"
2. Wait for the build to complete (2-5 minutes)
3. Once deployed, you'll get a URL like: `https://whiteboard-backend.onrender.com`
4. Check logs to ensure there are no errors

### Step 6: Update Frontend Configuration
Once deployed, update your frontend with:
```javascript
const BACKEND_URL = "https://whiteboard-backend.onrender.com";

// Socket.io connection
const socket = io(BACKEND_URL, {
  auth: {
    token: localStorage.getItem("token")
  }
});

// API calls
const response = await fetch(`${BACKEND_URL}/api/users/login`, {...});
```

## Verification

### Test API Endpoints
```bash
# Test register
curl -X POST https://whiteboard-backend.onrender.com/api/users/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'

# Test login
curl -X POST https://whiteboard-backend.onrender.com/api/users/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'
```

## Troubleshooting

### Issue: Application Error (H13)
- **Solution**: Check if PORT is set correctly and MONGO_URL is valid

### Issue: MongoDB Connection Timeout
- **Solution**: Ensure IP is whitelisted in MongoDB Atlas (use 0.0.0.0/0 for all IPs)

### Issue: Socket.io Connection Failed
- **Solution**: Ensure CLIENT_URL is set correctly in environment variables

### Issue: 401 Unauthorized on Socket
- **Solution**: Ensure SECRET_KEY matches between frontend and backend

## Environment Variables Summary

```env
# Production (.env for Render)
PORT=5000
NODE_ENV=production
MONGO_URL=mongodb+srv://admin:password@cluster.mongodb.net/whiteboard?retryWrites=true&w=majority
SECRET_KEY=your-very-secure-random-key-here
CLIENT_URL=https://your-frontend-domain.vercel.app
```

## Files Modified

- ✅ `server.js` - Updated SECRET_KEY and Socket.io config
- ✅ `controllers/userController.js` - Updated SECRET_KEY
- ✅ `middlewares/authMiddleware.js` - Updated SECRET_KEY
- ✅ `package.json` - Added metadata and Node version
- ✅ `render.yaml` - Created deployment config

## Next Steps

1. Generate a secure SECRET_KEY
2. Set up MongoDB Atlas
3. Push code to GitHub
4. Deploy on Render
5. Update frontend with backend URL
6. Deploy frontend on Vercel
7. Test the complete application
