# 🚀 ZERONE - Vercel Deployment Guide

## 📋 **Prerequisites**

- ✅ GitHub repository: https://github.com/Panchu11/zerone
- ✅ Vercel account (sign up at https://vercel.com)
- ✅ Frontend code in `zerone-frontend` directory
- ✅ All dependencies installed

---

## 🎯 **Step-by-Step Deployment**

### **Step 1: Prepare Your Vercel Account**

1. Go to https://vercel.com
2. Click "Sign Up" or "Log In"
3. Choose "Continue with GitHub"
4. Authorize Vercel to access your GitHub account

### **Step 2: Import Your Project**

1. Click "Add New..." → "Project"
2. Select "Import Git Repository"
3. Find and select `Panchu11/zerone`
4. Click "Import"

### **Step 3: Configure Build Settings**

**IMPORTANT**: Configure these settings before deploying:

#### **Framework Preset**
- Select: `Next.js`

#### **Root Directory**
- Set to: `zerone-frontend`
- Click "Edit" next to "Root Directory"
- Type: `zerone-frontend`
- Click "Continue"

#### **Build and Output Settings**
- Build Command: `npm run build` (auto-detected)
- Output Directory: `.next` (auto-detected)
- Install Command: `npm install` (auto-detected)

#### **Node.js Version**
- Version: `20.x` (recommended for Next.js 16)

### **Step 4: Environment Variables**

Add these environment variables (if needed):

```
NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID=your_project_id_here
NEXT_PUBLIC_ENABLE_TESTNETS=true
```

**To get WalletConnect Project ID:**
1. Go to https://cloud.walletconnect.com
2. Sign up/Login
3. Create a new project
4. Copy the Project ID
5. Paste it in Vercel environment variables

### **Step 5: Deploy**

1. Click "Deploy"
2. Wait for the build to complete (2-5 minutes)
3. ✅ Your app will be live at: `https://zerone-[random].vercel.app`

---

## 🔧 **Post-Deployment Configuration**

### **Custom Domain (Optional)**

1. Go to your project settings
2. Click "Domains"
3. Add your custom domain
4. Follow DNS configuration instructions

### **Environment Variables Management**

1. Go to Project Settings → Environment Variables
2. Add/Edit variables as needed
3. Redeploy for changes to take effect

---

## 🎨 **Vercel Configuration File**

A `vercel.json` file has been created in `zerone-frontend` with optimal settings:

```json
{
  "buildCommand": "npm run build",
  "devCommand": "npm run dev",
  "installCommand": "npm install",
  "framework": "nextjs",
  "outputDirectory": ".next"
}
```

---

## 🐛 **Troubleshooting**

### **Build Fails**

**Error**: `Module not found` or `Cannot find module`
- **Solution**: Make sure all dependencies are in `package.json`
- Run `npm install` locally to verify

**Error**: `Build exceeded maximum duration`
- **Solution**: Optimize build by removing unused dependencies
- Check for large files in `node_modules`

**Error**: `Root directory not found`
- **Solution**: Make sure "Root Directory" is set to `zerone-frontend`

### **Runtime Errors**

**Error**: `Hydration failed`
- **Solution**: Check for client-side only code in server components
- Use `'use client'` directive where needed

**Error**: `Network request failed`
- **Solution**: Check that contract addresses are correct
- Verify Sepolia RPC endpoints are accessible

### **Wallet Connection Issues**

**Error**: `WalletConnect not working`
- **Solution**: Add `NEXT_PUBLIC_WALLETCONNECT_PROJECT_ID` environment variable
- Redeploy after adding the variable

---

## 📊 **Deployment Checklist**

Before deploying, verify:

- [ ] All files pushed to GitHub
- [ ] `package.json` has all dependencies
- [ ] No hardcoded localhost URLs
- [ ] Environment variables configured
- [ ] Root directory set to `zerone-frontend`
- [ ] Build succeeds locally (`npm run build`)
- [ ] No TypeScript errors (`npm run lint`)

---

## 🚀 **Automatic Deployments**

Vercel automatically deploys when you push to GitHub:

- **Production**: Pushes to `main` branch
- **Preview**: Pushes to other branches or pull requests

### **To Trigger a New Deployment:**

```bash
# Make a change
git add .
git commit -m "Update: description of changes"
git push origin main
```

Vercel will automatically detect the push and redeploy!

---

## 🔗 **Useful Links**

- **Vercel Dashboard**: https://vercel.com/dashboard
- **Vercel Docs**: https://vercel.com/docs
- **Next.js on Vercel**: https://vercel.com/docs/frameworks/nextjs
- **Your GitHub Repo**: https://github.com/Panchu11/zerone

---

## 🎯 **Expected Deployment URL**

After deployment, your app will be available at:

```
https://zerone-[random-string].vercel.app
```

You can also set a custom domain like:
```
https://zerone.yourdomain.com
```

---

## 📱 **Testing Your Deployment**

1. Open the Vercel URL in your browser
2. Connect your MetaMask wallet (Sepolia network)
3. Test all features:
   - ✅ Vault deposit/withdraw
   - ✅ Aave supply/withdraw
   - ✅ Swap operations
   - ✅ Rebalancer activation
   - ✅ ZK proof generation

---

## 🎉 **Success!**

Your ZERONE app is now live on Vercel! 🚀

Share your deployment URL:
- In your demo video
- In your Zama Builder's Program submission
- On social media

---

## 🔄 **Continuous Deployment**

Every time you push to GitHub:
1. Vercel detects the change
2. Automatically builds the project
3. Deploys to production (if on `main` branch)
4. Sends you a notification

**No manual deployment needed!** 🎉

---

## 📞 **Support**

If you encounter issues:
1. Check Vercel deployment logs
2. Review build output for errors
3. Check Vercel documentation
4. Contact Vercel support (if needed)

---

**Built for Zama Builder's Program 2025** 🔐

