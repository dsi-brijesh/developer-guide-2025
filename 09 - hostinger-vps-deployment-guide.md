# Complete Step-by-Step Guide: Hostinger VPS Deployment with CloudPanel, GitHub Actions & PM2

This comprehensive guide will help you set up automated deployment for your web application using GitHub Actions, SSH Deploy Keys, CloudPanel, and PM2 on a Hostinger VPS.

---

## Architecture Overview

```
[ Developer Push ] ──► [ GitHub Repo ] ──► [ GitHub Actions Workflow ]
                                                     │ (SSH via Private Key)
                                                     ▼
                                          [ Hostinger VPS / CloudPanel ]
                                                     │ (git pull / build)
                                                     ▼
                                           [ PM2 Process Manager ]
```

---

## STEP 1: Generate SSH Key Pair on VPS via PuTTY / SSH

1. Connect to your Hostinger VPS using **PuTTY** or standard SSH:
   ```bash
   ssh root@<YOUR_VPS_IP>
   # Or connect directly as your site user (e.g. clp-user)
   ```

2. Switch to the site user (if connected as root) or stay as site user:
   ```bash
   su - clp-user
   ```

3. Generate a dedicated SSH key pair for GitHub deployment:
   ```bash
   ssh-keygen -t ed25519 -C "deploy-key-hostinger-vps" -f ~/.ssh/github_deploy_key
   ```
   - Press **Enter** for passphrase (leave empty for automated CI/CD access).

4. Fix permissions on the `.ssh` folder and keys:
   ```bash
   chmod 700 ~/.ssh
   chmod 600 ~/.ssh/github_deploy_key
   chmod 644 ~/.ssh/github_deploy_key.pub
   ```

5. Display the **Public Key** (copy this for GitHub Deploy Key):
   ```bash
   cat ~/.ssh/github_deploy_key.pub
   ```

6. Display the **Private Key** (copy this for GitHub Repository Secret):
   ```bash
   cat ~/.ssh/github_deploy_key
   ```

---

## STEP 2: Configure Keys in GitHub

### A. Add Deploy Key (Read Access for Git Pull)
1. Go to your **GitHub Repository** -> **Settings** -> **Deploy Keys**.
2. Click **Add deploy key**.
3. **Title**: `Hostinger VPS Deploy Key`
4. **Key**: Paste the content of `github_deploy_key.pub` (from Step 1.5).
5. Leave "Allow write access" **UNCHECKED**.
6. Click **Add key**.

### B. Add Repository Secrets (For GitHub Actions Workflow)
1. In your **GitHub Repository**, go to **Settings** -> **Secrets and variables** -> **Actions**.
2. Click **New repository secret** and add the following:

| Secret Name | Value | Example |
| :--- | :--- | :--- |
| `VPS_HOST` | Your VPS IP address or Domain | `185.xxx.xxx.xxx` |
| `VPS_USERNAME` | CloudPanel site user or SSH user | `clp-user` or `htdocs` user |
| `VPS_PORT` | SSH Port (Default: 22) | `22` |
| `VPS_SSH_KEY` | Entire content of `github_deploy_key` (Private key) | `-----BEGIN OPENSSH PRIVATE KEY----- ...` |
| `PROJECT_PATH` | Full absolute path to site directory on VPS | `/home/clp-user/htdocs/yourdomain.com` |

---

## STEP 3: Configure CloudPanel & SSH Host Key on VPS

1. On your VPS, authorize the private key so GitHub Actions can log in via SSH:
   ```bash
   cat ~/.ssh/github_deploy_key.pub >> ~/.ssh/authorized_keys
   chmod 600 ~/.ssh/authorized_keys
   ```

2. Add GitHub to `known_hosts` so automated scripts don't get prompt errors:
   ```bash
   ssh-keyscan -t ed25519 github.com >> ~/.ssh/known_hosts
   chmod 644 ~/.ssh/known_hosts
   ```

3. Tell SSH to use the deploy key when connecting to GitHub:
   Create or edit `~/.ssh/config`:
   ```bash
   nano ~/.ssh/config
   ```
   Add the following content:
   ```ssh
   Host github.com
     HostName github.com
     User git
     IdentityFile ~/.ssh/github_deploy_key
     IdentitiesOnly yes
   ```
   Save (`Ctrl+O`, `Enter`) and Exit (`Ctrl+X`).
   Set permissions:
   ```bash
   chmod 600 ~/.ssh/config
   ```

4. Test git SSH connectivity to GitHub:
   ```bash
   ssh -T git@github.com
   ```
   *Expected Output*: `Hi username/repo! You've successfully authenticated...`

---

## STEP 4: Initial Repository Clone on VPS (CloudPanel Directory)

1. Navigate to your website's root directory managed by CloudPanel:
   ```bash
   cd /home/clp-user/htdocs/yourdomain.com
   ```

2. Clone your repository into the directory (or clone into temp folder and move):
   ```bash
   # If folder is empty:
   git clone git@github.com:YOUR_ORGANIZATION/YOUR_REPO.git .

   # If folder already has files:
   git init
   git remote add origin git@github.com:YOUR_ORGANIZATION/YOUR_REPO.git
   git fetch origin
   git checkout -f main
   ```

3. Setup environment variables file (`.env`):
   ```bash
   cp .env.example .env
   nano .env
   # Add your production database credentials, API keys, etc.
   ```

---

## STEP 5: Setup PM2 for Node.js Application Management

1. Install PM2 globally (if not already installed):
   ```bash
   npm install -g pm2
   ```

2. Install project dependencies and build the application:
   ```bash
   npm install
   npm run build
   ```

3. Create an Ecosystem file `ecosystem.config.js` in your project root:
   ```javascript
   module.exports = {
     apps: [
       {
         name: "booking-web",
         script: "npm",
         args: "start",
         cwd: "/home/clp-user/htdocs/yourdomain.com",
         instances: 1,
         autorestart: true,
         watch: false,
         env: {
           NODE_ENV: "production",
           PORT: 3000
         }
       }
     ]
   };
   ```

4. Start your application with PM2:
   ```bash
   pm2 start ecosystem.config.js
   pm2 save
   ```

5. Ensure PM2 starts automatically on server reboots:
   ```bash
   pm2 startup
   # Copy and execute the command output printed by PM2
   ```

---

## STEP 6: Create GitHub Actions Workflow (`.github/workflows/deploy.yml`)

In your local project codebase, create a new file `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Hostinger VPS

on:
  push:
    branches:
      - main

jobs:
  deploy:
    name: Deploy Application to VPS
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v4

      - name: Deploy via SSH
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.VPS_HOST }}
          username: ${{ secrets.VPS_USERNAME }}
          key: ${{ secrets.VPS_SSH_KEY }}
          port: ${{ secrets.VPS_PORT }}
          script: |
            export PATH=$PATH:/usr/local/bin:/usr/bin:~/.nvm/versions/node/$(ls ~/.nvm/versions/node 2>/dev/null | tail -n 1)/bin
            cd ${{ secrets.PROJECT_PATH }}
            
            echo "1. Fetching latest changes from GitHub..."
            git pull origin main
            
            echo "2. Installing dependencies..."
            npm ci --production=false
            
            echo "3. Building project..."
            npm run build
            
            echo "4. Reloading PM2 application..."
            pm2 reload ecosystem.config.js || pm2 start ecosystem.config.js
            pm2 save
            
            echo "Deployment Completed Successfully!"
```

---

## STEP 7: CloudPanel Reverse Proxy Configuration

1. Log into your **CloudPanel Admin Dashboard** (`https://<YOUR_VPS_IP>:8443`).
2. Go to **Sites** -> Select `yourdomain.com`.
3. Under **Site Settings**, check the site type:
   - If configured as **Node.js**: Set **App Port** to `3000` (matching PM2 PORT).
   - If configured as **Reverse Proxy**: Set the reverse proxy target to `http://127.0.0.1:3000`.
4. Enable **SSL / Let's Encrypt Certificate** in CloudPanel under **SSL Certificates**.

---

## Troubleshooting & Verification Checklist

- [ ] **SSH Access Test**: Run `ssh -i ~/.ssh/github_deploy_key git@github.com` from VPS terminal.
- [ ] **Permissions Check**: Ensure `~/.ssh` is `700` and `github_deploy_key` is `600`.
- [ ] **PM2 Monitoring**: Check application status anytime using `pm2 status` or logs using `pm2 logs booking-web`.
- [ ] **GitHub Action Run**: Push a commit to `main` branch and verify successful execution under **GitHub Actions** tab.
