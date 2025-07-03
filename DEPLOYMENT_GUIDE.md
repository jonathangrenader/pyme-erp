# Deployment Guide

This document provides a step-by-step guide for deploying the pyme-erp application. It covers local setup, GitHub integration, and deployment to Firebase Hosting.

## 1. Local Development Environment Setup

Before you can deploy, ensure your local development environment is correctly set up.

### 1.1 Prerequisites
- Node.js (LTS version recommended)
- npm (Node Package Manager) or Yarn
- Git
- Firebase CLI (Command Line Interface)

### 1.2 Install Firebase CLI
If you don't have it installed globally, open your terminal and run:
```bash
npm install -g firebase-tools
```

### 1.3 Login to Firebase CLI
Login with your Google account. If you need to use a different account than your default, log out first.
```bash
firebase logout
firebase login
```
Follow the prompts in your browser to authenticate.

### 1.4 Clone the Repository (if starting fresh)
If you are setting up on a new machine, clone the project:
```bash
git clone [YOUR_GITHUB_REPO_URL]
cd pyme-erp
```

### 1.5 Install Project Dependencies
Navigate to the `pyme-erp` directory and install the Node.js dependencies for both the React app and Firebase Functions:
```bash
cd pyme-erp
npm install
cd functions
npm install
cd ..
```

### 1.6 Initialize Firebase Project (Already Done)
This step was performed during the initial setup. It creates `firebase.json` and `.firebaserc` files.

If you need to re-initialize or connect to a different project, run:
```bash
firebase init
```
**Remember to select:**
- Features: `Firestore`, `Functions`, `Hosting`
- Public directory for Hosting: `build`
- Configure as single-page app: `Yes`
- Do NOT set up automatic builds and deploys with GitHub via `firebase init`.

### 1.7 Run the Development Server
To run the React development server locally:
```bash
npm start
```

## 2. GitHub Integration

Your project is already a Git repository. We will now ensure it's connected to GitHub.

### 2.1 Create a GitHub Repository
- Go to GitHub and create a new **empty** repository (do NOT initialize with a README, .gitignore, or license).
- Name it `pyme-erp` (or your chosen project name).

### 2.2 Link Local Repository to GitHub
From your `pyme-erp` directory, run the following commands to link your local repository to the new GitHub repository and push your initial code:
```bash
git remote add origin [YOUR_GITHUB_REPO_URL]
git branch -M main
git push -u origin main
```
*(Replace `[YOUR_GITHUB_REPO_URL]` with the HTTPS URL of your GitHub repository, e.g., `https://github.com/your-username/pyme-erp.git`)*

## 3. Deployment to Firebase Hosting

Firebase Hosting provides fast, secure, and reliable hosting for your web application.

### 3.1 Build the React Application
Before deploying, you need to build your React application for production. This creates the optimized static files in the `build` directory.
```bash
npm run build
```

### 3.2 Deploy to Firebase
This command deploys your static files (from the `build` directory) and your Firebase Functions to your Firebase project.
```bash
firebase deploy
```

### 3.3 Continuous Deployment (Optional, Recommended)
For continuous deployment (automatically deploying every time you push to GitHub), you can set up a GitHub Action.

**Steps:**
1.  **Generate a Firebase Token:**
    ```bash
    firebase login:ci
    ```
    This will give you a token. Copy it.
2.  **Add Token to GitHub Secrets:**
    - Go to your GitHub repository settings (`Settings` > `Secrets` > `Actions`).
    - Add a new repository secret named `FIREBASE_TOKEN` and paste the token you copied.
3.  **Create GitHub Workflow File:**
    - In your GitHub repository, create a file at `.github/workflows/firebase-hosting.yml`.
    - Add the following content to the file:
    ```yaml
    name: Deploy to Firebase Hosting

on:
  push:
    branches:
      - main # or your main branch name

jobs:
  build_and_deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Use Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18' # or your desired Node.js version
      - name: Install Dependencies
        run: npm install
      - name: Build React App
        run: npm run build
      - name: Deploy to Firebase
        uses: FirebaseExtended/action-hosting-deploy@v0
        with:
          repoToken: '${{ secrets.GITHUB_TOKEN }}'
          firebaseServiceAccount: '${{ secrets.FIREBASE_SERVICE_ACCOUNT_PYME_ERP }}' # Replace PYME_ERP with your project ID
          channelId: live
          projectId: pyme-erp # Replace with your project ID
    ```
    *(Note: You will need to create a `FIREBASE_SERVICE_ACCOUNT_PYME_ERP` secret in GitHub with the service account key JSON from your Firebase project. This is more secure than `FIREBASE_TOKEN` for CI/CD.)*

## 4. Initial SUPERADMIN Setup

After the first successful deployment, you will need to manually set your user as `SUPERADMIN`.

1.  **Get your User UID:** Log in to your deployed app, then go to Firebase Console -> Authentication. Find your user and copy their UID.
2.  **Set Custom Claim:** In your local terminal, run:
    ```bash
    firebase auth:set-custom-user-claims [YOUR_USER_UID] '{"isSuperAdmin": true}'
    ```
    *(Replace `[YOUR_USER_UID]` with your actual user UID.)*

This will grant your user account SUPERADMIN privileges, allowing access to global management features.
