# 🚀 CI/CD Pipeline with GitHub Actions (PHP + Hostinger)

This repository demonstrates a **CI/CD pipeline setup** for deploying a PHP app to **Hostinger shared hosting** using **GitHub Actions**.  
It supports **multi-environment deployments**: `staging` and `production`.

---

## 🔑 Repository Secrets Setup

Before running workflows, configure the following **Repository Secrets** in your GitHub repo:

| Secret Name       | Example Value                                            | Description                                       |
|-------------------|----------------------------------------------------------|---------------------------------------------------|
| `SSH_HOST`        | `123.123.123.123` or `ssh.hostinger.com`                 | Server hostname or IP                             |
| `SSH_PORT`        | `22` (or custom port e.g., `65002`)                      | SSH port                                          |
| `SSH_USER`        | `u123456`                                                | SSH username (Hostinger provides this)            |
| `SSH_KEY`         | *(paste contents of your private key file)*              | Private key used for deployment                   |
| `SSH_KNOWN_HOSTS` | Output of `ssh-keyscan -p SSH_PORT SSH_HOST`             | Known hosts fingerprint                           |
| `STAGING_PATH`    | `/home/u123456/public_html/staging`                      | Absolute path to staging site                     |
| `PRODUCTION_PATH` | `/home/u123456/public_html/production`                   | Absolute path to production (live) site           |

---

## 🌿 Git Branch Strategy

We use a **three-branch workflow**:

- **`master`** → Deploys to **Staging**
- **`prod`** → Deploys to **Production (Live)**
- **Feature branches** → Local development, merged into `master`

### Flow:
feature → master (staging) → prod (production/live)


This ensures staging always mirrors `master`, and production always mirrors `prod`.

---

## ⚙️ Workflows Overview

### 1. **Deploy Staging**
- **Trigger**: Push/merge to `master`
- **Action**: Deploys code to `STAGING_PATH`

### 2. **Promote Master to Prod**
- **Trigger**: Manually from GitHub Actions tab
- **Action**: Resets `prod` branch to match `master` (clean sync, no merge conflicts)
- **Result**: Push to `prod` automatically triggers Production deploy

### 3. **Deploy Production**
- **Trigger**: Push to `prod` branch
- **Action**: Deploys code to `PRODUCTION_PATH`

---

## 📦 Deployment Process

Both staging and production deployments use the same process:

1. Create a **tarball release** of your code (excluding `.git`, `.github`, etc.).
2. Upload the package via `scp` to your Hostinger server.
3. Extract it into a **timestamped release directory**: /releases/20250905132258
4. Update the `current` symlink to point to the new release.
5. Generate a proper `.env` file for the environment (`staging` or `production`).

---

## 🔐 Environment Files

We manage environment configuration with multiple `.env` files:

- `.env.local` → For local development
- `.env.staging` → Used in staging deploy (`APP_ENV=staging`)
- `.env.production` → Used in production deploy (`APP_ENV=production`)

During deployment, the pipeline automatically injects the correct environment variables into the `.env` file inside the release directory.

---

## 🚀 Typical Workflow

1. Create a **feature branch** and develop locally.  
2. Open a **Pull Request** into `master`.  
3. Merge PR → Deploys automatically to **staging**.  
4. Test staging.  
5. Manually run **Promote Master to Prod** from the Actions tab.  
6. This triggers **Deploy Production** workflow → code goes live.  

---

## 📝 Notes

- Ensure your `SSH_KEY` matches the public key uploaded in your Hostinger panel.  
- You can test connection manually with:  
```bash
ssh -p <SSH_PORT> <SSH_USER>@<SSH_HOST>


The pipeline is written for PHP, but can be adapted for Laravel, Node.js, or other apps.

## 📺 Tutorial

This repository is part of a YouTube tutorial that walks through setting up CI/CD with GitHub Actions on Hostinger shared hosting step by step.
