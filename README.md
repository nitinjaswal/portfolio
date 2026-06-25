# 🚀 Portfolio Site — Azure Blob Storage + CDN

Hosted on **Azure Blob Static Website** with **Azure CDN** for global delivery.
Auto-deployed via **GitHub Actions** on every push to `main`.

---

## 📁 Project Structure

```
portfolio/
├── index.html                        ← your portfolio page
├── resume.pdf                        ← your resume (add this!)
├── assets/                           ← images, icons etc
└── .github/
    └── workflows/
        └── deploy.yml                ← GitHub Actions pipeline
```

---

## ⚙️ One-Time Azure Setup

### 1. Create Resource Group & Storage Account

```bash
az group create --name portfolio-rg --location eastus

az storage account create \
  --name yourportfoliosite \
  --resource-group portfolio-rg \
  --sku Standard_LRS \
  --kind StorageV2
```

### 2. Enable Static Website

```bash
az storage blob service-properties update \
  --account-name yourportfoliosite \
  --static-website \
  --index-document index.html \
  --404-document 404.html
```

### 3. Create Azure CDN

```bash
az cdn profile create \
  --name portfolio-cdn \
  --resource-group portfolio-rg \
  --sku Standard_Microsoft

az cdn endpoint create \
  --name portfolio-endpoint \
  --profile-name portfolio-cdn \
  --resource-group portfolio-rg \
  --origin yourportfoliosite.z13.web.core.windows.net \
  --origin-host-header yourportfoliosite.z13.web.core.windows.net
```

### 4. Create Service Principal for GitHub Actions

```bash
az ad sp create-for-rbac \
  --name "github-portfolio-deploy" \
  --role contributor \
  --scopes /subscriptions/<YOUR_SUBSCRIPTION_ID>/resourceGroups/portfolio-rg \
  --sdk-auth
```

Copy the JSON output — you'll need it next.

---

## 🔑 GitHub Secrets to Add

Go to: **GitHub repo → Settings → Secrets → Actions → New secret**

| Secret Name | Value |
|---|---|
| `AZURE_CREDENTIALS` | JSON output from service principal command |
| `STORAGE_ACCOUNT_NAME` | `yourportfoliosite` |
| `RESOURCE_GROUP` | `portfolio-rg` |
| `CDN_PROFILE_NAME` | `portfolio-cdn` |
| `CDN_ENDPOINT_NAME` | `portfolio-endpoint` |

---

## 🚀 Deploy

```bash
git add .
git commit -m "update portfolio"
git push origin main
```

GitHub Actions runs automatically → uploads files → purges CDN → live! ✅

---

## ✏️ Before Going Live — Update These

In `index.html` replace:
- `Your Name` → your actual name
- `you@email.com` → your email
- `yourusername` → your GitHub username
- `yourprofile` → your LinkedIn profile
- Stats (years experience, projects count)

Add your `resume.pdf` to the root folder.

---

## 🌐 Your Live URLs

| URL | What it is |
|---|---|
| `https://yourportfoliosite.z13.web.core.windows.net` | Blob Storage direct URL |
| `https://portfolio-endpoint.azureedge.net` | CDN URL (faster, use this) |
