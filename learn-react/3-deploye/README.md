# Deploy Your React Project to GitHub Pages Using YAML

## Introduction

Deploying your React project to GitHub Pages can be automated using GitHub Actions. This guide will walk you through setting up a **Vite React** project, creating a repository, and configuring GitHub Actions to deploy your project automatically.

---

## Step 1: Create a Vite React App

If you haven't already created a Vite React project, follow these steps:

```sh
# Install pnpm globally (optional but recommended for faster installs)
npm install -g pnpm

# Create a new Vite project
pnpm create vite@latest app-2 --template react

# Navigate to the project directory
cd app-2

# Install dependencies
pnpm install
```

This will set up a React project with Vite as the bundler.

---

## Step 2: Create a GitHub Repository

1. Go to [GitHub](https://github.com/).
2. Click on **New Repository**.
3. Enter a repository name (e.g., `app-2`).
4. Choose **Public** or **Private**.
5. Click **Create Repository**.
6. Follow the instructions to push your local project:

```sh
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/your-username/app-2.git
git push -u origin main
```

---

## Step 3: Configure Vite for GitHub Pages

By default, Vite serves assets with absolute paths (`/`). Since GitHub Pages serves projects under `https://your-username.github.io/repo-name/`, we need to update `vite.config.js` to set the correct base path.

### Edit `vite.config.js`

```js
import { defineConfig } from "vite";
import react from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [react()],
  base: "/app-2", // Change this to your repo name
});
```

Commit and push the changes:

```sh
git add vite.config.js
git commit -m "Set base path for GitHub Pages"
git push origin main
```

---

## Step 4: Setup GitHub Actions Workflow

To automate deployment, create a **GitHub Actions workflow** inside your repository.

### Create `.github/workflows/deploy.yml`

Inside your project, create a **`.github/workflows/deploy.yml`** file and add the following configuration:

```yml
name: Deploy

on:
  push:
    branches:
      - main

jobs:
  build:
    name: Build
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repo
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Install dependencies
        uses: bahmutov/npm-install@v1

      - name: Build project
        run: npm run build

      - name: Upload production-ready build files
        uses: actions/upload-artifact@v4
        with:
          name: production-files
          path: ./dist

  deploy:
    name: Deploy
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    steps:
      - name: Download artifact
        uses: actions/download-artifact@v4
        with:
          name: production-files
          path: ./dist

      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

Commit and push the workflow file:

```sh
git add .github/workflows/deploy.yml
git commit -m "Add GitHub Actions for deployment"
git push origin main
```

---

## Step 5: Enable GitHub Actions Permissions

1. Go to **GitHub Repository > Settings**.
2. Click on **Actions**.
3. Under **General**, find **Workflow Permissions**.
4. Select **Read and write permissions**.
5. Click **Save**.

This allows GitHub Actions to deploy to your repository.

---

## Step 6: Trigger Deployment

1. Go to your repository on GitHub.
2. Click on the **Actions** tab.
3. Select the most recent workflow run.
4. Wait for the **Deploy** step to complete.

Once it's done, your site is deployed!

---

## Step 7: Configure GitHub Pages

1. Go to **GitHub Repository > Settings > Pages**.
2. Under **Branch**, select `gh-pages`.
3. Select `/ (root)` as the source.
4. Click **Save**.

Your site will be available at:

```
https://your-username.github.io/app-2/
```

---

## Step 8: Verify Deployment

1. Open your deployed site in a browser.
2. If styles or assets are missing, **clear cache and refresh**.
3. If you see a `404` error for CSS/JS files, ensure `vite.config.js` has the correct `base`.

---

## Conclusion

Congratulations! 🎉 You've successfully automated the deployment of your Vite React app to GitHub Pages using GitHub Actions. Now, every time you push changes to `main`, your site will automatically update.

**Next Steps:**

- Add a custom domain under **Settings > Pages**.
- Improve CI/CD workflows with **linting and testing**.
- Explore **GitHub Actions for more automation**.

Happy coding! 🚀
