# ⚡ Quick Fix: Vercel WebSocket Issue

## 🔴 The Problem

**Vercel doesn't support persistent WebSocket connections (Socket.io)**

Your backend on Vercel will:
- ❌ Connect briefly (few seconds)
- ❌ Drop connection when serverless function terminates
- ❌ Never stay connected long-term

**This is a Vercel limitation, NOT a bug in your code.**

---

## ✅ The Solution (5 Minutes)

### Move Backend to Render (keeps WebSockets alive 24/7)

**Frontend stays on Vercel** → Frontend works perfectly there!

---

## 🚀 Step-by-Step Fix

### 1. Sign up for Render
- Go to: https://render.com
- Click "Get Started for Free"
- Sign up with GitHub

### 2. Deploy Backend on Render

**Using Blueprint (Easiest):**
1. In Render dashboard, click "New +" → "Blueprint"
2. Connect to your GitHub repo: `DineshDumka/CodeMesh`
3. Render will detect `render.yaml` automatically
4. Click "Apply" - it will auto-configure everything!
5. Wait 3-5 minutes for deployment
6. Copy your backend URL: `https://code-mesh-server.onrender.com`

**Manual Setup (if blueprint doesn't work):**
1. Click "New +" → "Web Service"
2. Connect GitHub → Select `DineshDumka/CodeMesh`
3. Configure:
   - Name: `code-mesh-server`
   - Root Directory: `server`
   - Build: `npm install && npm run build`
   - Start: `npm start`
   - Plan: Free
4. Add Environment Variables:
   - `PORT` = `10000`
   - `NODE_ENV` = `production`
5. Click "Create Web Service"
6. Wait for deployment
7. Copy backend URL

### 3. Update Frontend on Vercel

1. Go to Vercel dashboard
2. Select `code-mesh-client` project
3. Settings → Environment Variables
4. Update `VITE_BACKEND_URL` to your Render backend URL:
   ```
   VITE_BACKEND_URL=https://code-mesh-server.onrender.com
   ```
5. Deployments tab → Redeploy

### 4. Configure CORS on Render

1. Go to Render dashboard → Your backend service
2. Environment tab → Add Environment Variable
3. Add:
   ```
   CORS_ORIGIN=https://code-mesh-client-dineshdumka.vercel.app
   ```
4. Save (auto-redeploys)

### 5. Delete Old Vercel Backend (Optional)

Since it doesn't work anyway:
1. Go to Vercel dashboard
2. Select `code-mesh-server` project
3. Settings → Delete Project

---

## ✅ Test It

1. Visit: https://code-mesh-client-dineshdumka.vercel.app
2. Create a room
3. Join with username
4. Connection should stay stable! ✨
5. Test in 2 tabs - collaboration should work perfectly

---

## 📊 Final Architecture

```
✅ Frontend (Vercel)
   ↓
   WebSocket Connection
   ↓
✅ Backend (Render)
```

**Both free tiers!**

---

## ⚠️ Free Tier Note

Render free tier sleeps after 15 min inactivity:
- First request wakes it (~30 seconds)
- After that, works perfectly
- For always-on: Upgrade to Starter ($7/mo)

**Optional:** Add keep-alive ping (see RENDER_DEPLOYMENT_GUIDE.md)

---

## 🎉 Done!

Your WebSocket connection will now:
- ✅ Stay connected
- ✅ Never drop randomly
- ✅ Work exactly like localhost
- ✅ Support real-time collaboration

**Full detailed guide:** See `RENDER_DEPLOYMENT_GUIDE.md`

---

## 🆘 Still Having Issues?

Check:
1. CORS_ORIGIN on Render matches exact frontend URL
2. VITE_BACKEND_URL on Vercel matches exact backend URL
3. Both services fully redeployed
4. Check Render logs for errors

**Render logs:** Dashboard → Your Service → Logs tab
