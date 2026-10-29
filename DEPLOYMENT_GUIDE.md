# Step-by-Step Cloud Deployment Guide

This guide walks you through deploying the complete **Three-Tier Student Information Management System** across **MongoDB Atlas**, **Render**, and **Netlify** using your GitHub account and MongoDB Atlas account.

---

## 📋 Phase 1: MongoDB Atlas Setup (Connected & Verified ✅)

Your MongoDB Atlas database has already been configured and verified:

1. **MongoDB Atlas Account:** `abhijeetshelke2004@gmail.com`
2. **Database Cluster:** `cluster0.8ryeicv.mongodb.net`
3. **Database User:** `abhijeetshelke2004_db_user`
4. **Database Name:** `studentDB`
5. **Collection Name:** `students`
6. **Network Access (IP Whitelist):**
   - In Atlas under **Security > Network Access**:
   - Ensure `0.0.0.0/0` (Allow Access from Anywhere) is enabled so Render servers can connect dynamically.
7. **Production Connection String:**
   ```text
   mongodb+srv://abhijeetshelke2004_db_user:jqSOaGxRilXzOCfJ@cluster0.8ryeicv.mongodb.net/studentDB?retryWrites=true&w=majority
   ```

---

## 🐙 Phase 2: Push Project to GitHub

1. Create a new repository on your GitHub account:
   - Go to [https://github.com/new](https://github.com/new).
   - Repository name: `student-management` (or `three-tier-student-management`).
   - Description: `Design and Deployment of a Three-Tier Web Application Using Netlify, Render, and MongoDB Atlas`.
   - Visibility: **Public**.
   - Do NOT check "Initialize this repository with a README".
2. Link your local project and push to your repository:
   ```bash
   git branch -M main
   git remote add origin https://github.com/<YOUR_GITHUB_USERNAME>/student-management.git
   git push -u origin main
   ```

---

## ⚡ Phase 3: Deploy Backend on Render (Tier 2 - Application Layer)

1. **Sign in to Render:**
   - Go to [https://dashboard.render.com](https://dashboard.render.com).
   - Sign in using your GitHub account.

2. **Create New Web Service:**
   - Click **New +** > **Web Service**.
   - Select **Build and deploy from a Git repository**.
   - Connect your repository: `<YOUR_GITHUB_USERNAME>/student-management`.

3. **Configure Service Details:**
   - **Name:** `student-management-api`
   - **Region:** Singapore / Frankfurt / Oregon (select closest to you).
   - **Branch:** `main`
   - **Root Directory:** `backend` (or leave empty if using root `package.json`).
   - **Runtime:** `Node`
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
   - **Plan:** `Free`

4. **Add Environment Variables:**
   Under **Environment Variables**, add:
   - `MONGODB_URI`: `mongodb+srv://abhijeetshelke2004_db_user:jqSOaGxRilXzOCfJ@cluster0.8ryeicv.mongodb.net/studentDB?retryWrites=true&w=majority`
   - `PORT`: `5000`
   - `NODE_ENV`: `production`
   - `FRONTEND_URL`: `*` (or your Netlify site URL once deployed).

5. **Deploy:**
   - Click **Create Web Service**.
   - Once deployed, copy your backend URL:
     `https://student-management-api.onrender.com`

---

## 🌐 Phase 4: Deploy Frontend on Netlify (Tier 1 - Presentation Layer)

1. **Sign in to Netlify:**
   - Go to [https://app.netlify.com](https://app.netlify.com).
   - Sign in using your GitHub account.

2. **Import Repository:**
   - Click **Add new site** > **Import an existing project**.
   - Select **GitHub** and authorize access to `<YOUR_GITHUB_USERNAME>/student-management`.

3. **Configure Build Settings:**
   - **Branch to deploy:** `main`
   - **Base directory:** `frontend`
   - **Build command:** *(leave empty)*
   - **Publish directory:** `frontend` (or `.` when base directory is `frontend`).

4. **Deploy Site:**
   - Click **Deploy site**.
   - Netlify will assign a public domain, e.g., `https://student-management-abhijeet.netlify.app`.

5. **Connect Frontend to Render Backend:**
   - Open your deployed Netlify site in your browser.
   - Click **API Config** in the top right navbar.
   - Enter your Render backend URL (e.g., `https://student-management-api.onrender.com`).
   - Click **Save URL** and **Test Connection**.

6. **Lock Down Backend CORS (Recommended for Production):**
   - In Render Dashboard > your service > **Environment Variables**:
   - Update `FRONTEND_URL` to your exact Netlify domain:
     `https://student-management-abhijeet.netlify.app`
   - Save changes to automatically redeploy Render.

---

## 🧪 Phase 5: Verification & End-to-End Test Cases

| Test Case | Action | Input Data | Expected Result |
| :--- | :--- | :--- | :--- |
| **Test Case 1** | Add Student | `ID: ST101`, `Name: Rahul Sharma`, `Department: Computer Science`, `Sem: 5`, `Contact: 9876543210` | "Student added successfully" toast, record appears in directory. |
| **Test Case 2** | Display Students | Reload page or click **Refresh** | All student records loaded from MongoDB Atlas via Render API. |
| **Test Case 3** | Search Student | Type `ST101` in the search bar | Table filters in real-time showing Rahul Sharma. |
| **Test Case 4** | Update Student | Click **Edit** icon, change `Semester` to `6` | "Student updated successfully", semester displays `Sem 6`. |
| **Test Case 5** | Delete Student | Click **Delete** icon, confirm deletion | "Student deleted successfully", record removed from table. |
| **Test Case 6** | MongoDB Atlas Verification | Open Atlas > Collections > `studentDB.students` | Confirm records reflect operations made from Netlify UI. |
