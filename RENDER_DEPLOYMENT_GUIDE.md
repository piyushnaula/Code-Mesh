# 🚀 Deploy Backend on Render + Frontend on Vercel

## Why This Setup?

- **Vercel:** Perfect for frontend (static sites, fast CDN)
- **Render:** Perfect for backend (supports WebSockets, persistent connections)

This is the **recommended architecture** for Socket.io applications.

---

## 📋 Architecture Overview

```
Frontend (Vercel)
    ↓
https://code-mesh-client-dineshdumka.vercel.app
    ↓
    ↓ WebSocket Connection
    ↓
Backend (Render)
    ↓
https://code-mesh-server.onrender.com
```

---

## 🎯 Step 1: Prepare Backend for Render

### 1.1 Update server/package.json

Verify your start script is correct:

```json
{
  "scripts": {
    "dev": "nodemon --exec ts-node src/server.ts",
    "start": "node dist/server.js",
    "build": "tsc"
  }
}
```

### 1.2 Verify TypeScript Config

Your `server/tsconfig.json` should output to `dist`:

```json
{
  "compilerOptions": {
    "outDir": "./dist",
    "rootDir": "./src"
  }
}
```

### 1.3 Add Engine Specification

In `server/package.json`, ensure you have:

```json
{
  "engines": {
    "node": ">=18.0.0"
  }
}
```

---

## 🌐 Step 2: Deploy Backend on Render

### Option A: Using Render Dashboard (Recommended)

1. **Create Render Account:**
   - Go to: https://render.com
   - Sign up with GitHub (free tier available)

2. **Create New Web Service:**
   - Click "New +" → "Web Service"
   - Connect your GitHub account if prompted
   - Select repository: `DineshDumka/CodeMesh`

3. **Configure the Service:**

   ```
   Name: code-mesh-server
   Region: Choose closest to your users (e.g., Oregon, Frankfurt)
   Branch: main
   Root Directory: server
   Runtime: Node
   Build Command: npm install && npm run build
   Start Command: npm start
   ```

4. **Select Plan:**
   - Choose **"Free"** tier (perfect for testing/small projects)
   - Note: Free tier sleeps after 15 min inactivity (wakes in ~30 seconds on first request)
   - For production without sleep: Choose **"Starter" ($7/month)**

5. **Add Environment Variables:**
   
   Click "Advanced" → "Add Environment Variable":
   
   ```
   Key: PORT
   Value: 10000
   
   Key: NODE_ENV
   Value: production
   ```
   
   **Note:** Don't add CORS_ORIGIN yet - we'll add it after getting the frontend URL

6. **Click "Create Web Service"**

7. **Wait for Deployment:**
   - Render will build and deploy your app
   - Takes 2-5 minutes
   - Watch the logs for any errors

8. **Copy Your Backend URL:**
   - After successful deployment, you'll get a URL like:
   - `https://code-mesh-server.onrender.com`
   - **Save this URL!**

---

## 🎨 Step 3: Update Frontend on Vercel

1. **Go to Vercel Dashboard:**
   - Visit: https://vercel.com/dashboard
   - Select your frontend project: `code-mesh-client`

2. **Update Environment Variables:**
   - Go to: Settings → Environment Variables
   - Find `VITE_BACKEND_URL`
   - Update value to: `https://code-mesh-server.onrender.com`
   - Click "Save"

3. **Redeploy Frontend:**
   - Go to: Deployments tab
   - Click the three dots (...) on latest deployment
   - Click "Redeploy"
   - Wait for redeployment to complete

4. **Copy Your Frontend URL:**
   - You should already have: `https://code-mesh-client-dineshdumka.vercel.app`

---

## 🔗 Step 4: Configure CORS on Backend

1. **Go to Render Dashboard:**
   - Select your `code-mesh-server` service

2. **Add CORS Environment Variable:**
   - Go to: Environment tab
   - Click "Add Environment Variable"
   - Add:
     ```
     Key: CORS_ORIGIN
     Value: https://code-mesh-client-dineshdumka.vercel.app
     ```
   - Click "Save Changes"

3. **Render Auto-Redeploys:**
   - Render automatically redeploys when env variables change
   - Wait for redeployment to complete (~2-3 minutes)

---

## ✅ Step 5: Test Your Deployment

1. **Visit Your Frontend:**
   - Go to: https://code-mesh-client-dineshdumka.vercel.app

2. **Test Connection:**
   - Try creating a room
   - Join with a username
   - If successful, you should see no errors!

3. **Test Collaboration:**
   - Open two browser tabs
   - Join same room with different usernames
   - Type code - should sync in real-time
   - Connection should stay stable!

4. **Check Backend Health:**
   - Visit: https://code-mesh-server.onrender.com
   - Should show the index.html page or a response

---

## 🐛 Troubleshooting

### Issue: Backend shows "Application failed to respond"

**Solution:**
- Check Render logs (Logs tab in dashboard)
- Verify build completed successfully
- Ensure `npm start` command is correct
- Check PORT is set to 10000

### Issue: Still getting "Failed to connect to server"

**Solution:**
- Verify CORS_ORIGIN matches EXACT frontend URL
- Check VITE_BACKEND_URL in frontend env variables
- Clear browser cache and try again
- Check Render logs for connection errors

### Issue: Connection works but then drops

**Solution:**
- If on Free tier, this is normal after 15 min inactivity
- Upgrade to Starter plan ($7/mo) for always-on service
- Or implement a keep-alive ping from frontend

### Issue: Build fails on Render

**Solution:**
- Check Node.js version in package.json
- Verify all dependencies are in package.json
- Check build logs for specific errors
- Try: `npm install && npm run build` locally first

---

## 💰 Render Pricing (As of 2025)

### Free Tier:
- ✅ 750 hours/month (enough for 1 service 24/7)
- ✅ Supports WebSockets
- ⚠️ Sleeps after 15 min inactivity
- ⚠️ Takes ~30 seconds to wake up
- Perfect for: Testing, demos, hobby projects

### Starter Tier ($7/month):
- ✅ Always-on (no sleep)
- ✅ Faster CPU
- ✅ More memory
- ✅ Better for production

---

## 🔄 Automatic Deployments

Render automatically deploys when you push to GitHub:

```bash
# Make changes locally
git add .
git commit -m "Update backend"
git push origin main

# Render automatically:
# 1. Detects the push
# 2. Builds your app
# 3. Deploys new version
# 4. Zero downtime
```

---

## 📊 Environment Variables Summary

### Backend (Render)
```env
PORT=10000
NODE_ENV=production
CORS_ORIGIN=https://code-mesh-client-dineshdumka.vercel.app
```

### Frontend (Vercel)
```env
VITE_BACKEND_URL=https://code-mesh-server.onrender.com
```

---

## 🎯 Alternative: Railway Deployment

If you prefer Railway over Render:

1. **Go to:** https://railway.app
2. **Click:** "Start a New Project"
3. **Select:** "Deploy from GitHub repo"
4. **Choose:** DineshDumka/CodeMesh
5. **Configure:**
   - Root Directory: `server`
   - Build Command: `npm install && npm run build`
   - Start Command: `npm start`
6. **Add Variables:** Same as Render (PORT, CORS_ORIGIN)
7. **Deploy:** Railway handles the rest

**Railway Pricing:**
- $5/month for 500 hours
- More expensive than Render but faster deployments

---

## 🚀 Performance Tips

### 1. Keep Backend Awake (Free Tier)

Add this to your frontend to ping backend every 10 minutes:

```typescript
// In your App.tsx or main component
useEffect(() => {
  const keepAlive = setInterval(() => {
    fetch(import.meta.env.VITE_BACKEND_URL)
      .catch(() => {}) // Ignore errors
  }, 10 * 60 * 1000) // Every 10 minutes
  
  return () => clearInterval(keepAlive)
}, [])
```

### 2. Add Health Check Endpoint

Already exists in your `server.ts`:
```typescript
app.get("/", (req, res) => {
  res.sendFile(path.join(__dirname, "..", "public", "index.html"))
})
```

### 3. Monitor Uptime

Use UptimeRobot (free) to ping your backend every 5 minutes:
- Keeps it awake on free tier
- Alerts you if it goes down

---

## 📝 Deployment Checklist

Before going live:

- [ ] Backend deployed on Render
- [ ] Frontend deployed on Vercel
- [ ] CORS_ORIGIN set correctly on backend
- [ ] VITE_BACKEND_URL set correctly on frontend
- [ ] Both services redeployed with new env vars
- [ ] Can create and join rooms
- [ ] Real-time collaboration works
- [ ] Connection stays stable (no disconnects)
- [ ] Chat functionality works
- [ ] File operations work
- [ ] Code execution works

---

## 🎉 Success!

Your Code Mesh is now deployed with:
- ✅ **Frontend on Vercel:** Fast CDN, global edge network
- ✅ **Backend on Render:** Persistent WebSocket connections, stable 24/7
- ✅ **Best of both worlds:** Speed + Reliability

**Frontend:** https://code-mesh-client-dineshdumka.vercel.app
**Backend:** https://code-mesh-server.onrender.com

---

## 📞 Need Help?

- **Render Docs:** https://render.com/docs
- **Render Community:** https://community.render.com
- **Check Logs:** Render Dashboard → Your Service → Logs tab

---

**Happy deploying! Your Socket.io app will now work perfectly! 🚀**
