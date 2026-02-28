# 🔐 BlackRoad Secrets Management Guide

**How to Securely Manage API Keys, Tokens, and Credentials**

> **Last Updated:** 2026-02-28
> **Maintained By:** BlackRoad OS, Inc.

---

## ⚠️ Golden Rule

> **NEVER commit secrets, API keys, or credentials to source code.**
>
> All secrets must be stored in **GitHub Secrets** (organization or repository level)
> or a secure vault solution. Violations will be flagged by secret scanning.

---

## 🏗️ Where Secrets Live

| Level | Scope | How to Set |
|-------|-------|------------|
| **Organization Secrets** | Shared across all repos in an org | GitHub Org → Settings → Secrets and variables → Actions |
| **Repository Secrets** | Specific to one repository | Repo → Settings → Secrets and variables → Actions |
| **Environment Secrets** | Scoped to a deployment environment | Repo → Settings → Environments → [env] → Secrets |

### Recommended Approach

Set shared secrets (Stripe, Cloudflare, Google) at the **organization level** so all repos
in that org can access them without duplication.

---

## 🔑 Required Secrets Reference

### Stripe (Commerce & Billing)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `STRIPE_SECRET_KEY` | Server-side API key (sk_live_...) | [Stripe Dashboard → API Keys](https://dashboard.stripe.com/apikeys) |
| `STRIPE_PUBLISHABLE_KEY` | Client-side key (pk_live_...) | [Stripe Dashboard → API Keys](https://dashboard.stripe.com/apikeys) |
| `STRIPE_WEBHOOK_SECRET` | Webhook endpoint signing secret (whsec_...) | [Stripe Dashboard → Webhooks](https://dashboard.stripe.com/webhooks) |

**Setup steps:**
1. Log in to [Stripe Dashboard](https://dashboard.stripe.com)
2. Navigate to Developers → API Keys
3. Copy the live keys (use test keys for staging)
4. Add them as GitHub Organization Secrets in `BlackRoad-Ventures`

### Google Drive (Backup & Sync)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `GOOGLE_SERVICE_ACCOUNT_KEY` | JSON key for Google service account | [Google Cloud Console → IAM → Service Accounts](https://console.cloud.google.com/iam-admin/serviceaccounts) |
| `GDRIVE_FOLDER_ID` | Target folder ID for backups | Google Drive folder URL (the ID after `/folders/`) |

**Setup steps:**
1. Create a service account in Google Cloud Console
2. Enable the Google Drive API
3. Download the JSON key file
4. Add the JSON content as `GOOGLE_SERVICE_ACCOUNT_KEY` in GitHub Secrets
5. Share the target Drive folder with the service account email
6. Add the folder ID as `GDRIVE_FOLDER_ID`

### Cloudflare (DNS, CDN, Workers)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `CLOUDFLARE_API_TOKEN` | Scoped API token | [Cloudflare Dashboard → API Tokens](https://dash.cloudflare.com/profile/api-tokens) |
| `CLOUDFLARE_ZONE_ID` | Zone ID for blackroad.io | Cloudflare Dashboard → domain → Overview (right sidebar) |
| `CLOUDFLARE_ACCOUNT_ID` | Account identifier | Cloudflare Dashboard → domain → Overview (right sidebar) |

### Salesforce (CRM)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `SALESFORCE_CLIENT_ID` | Connected App consumer key | Salesforce Setup → App Manager |
| `SALESFORCE_CLIENT_SECRET` | Connected App consumer secret | Salesforce Setup → App Manager |
| `SALESFORCE_REFRESH_TOKEN` | OAuth refresh token | OAuth flow after app authorization |
| `SALESFORCE_INSTANCE_URL` | Instance URL (e.g., https://na1.salesforce.com) | Salesforce login |

### Tailscale (Mesh Network)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `TAILSCALE_AUTH_KEY` | Auth key for joining tailnet | [Tailscale Admin → Settings → Keys](https://login.tailscale.com/admin/settings/keys) |

### Railway (Cloud Deployment)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `RAILWAY_TOKEN` | API token for deployments | [Railway Dashboard → Account → Tokens](https://railway.app/account/tokens) |

### HuggingFace (Model Registry)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `HF_TOKEN` | API token for model access | [HuggingFace → Settings → Access Tokens](https://huggingface.co/settings/tokens) |

### NPM (Package Publishing)

| Secret Name | Description | Where to Get |
|-------------|-------------|--------------|
| `NPM_TOKEN` | Automation token for publishing | [npmjs.com → Access Tokens](https://www.npmjs.com/settings/~/tokens) |

---

## 🛡️ Security Best Practices

1. **Rotate keys regularly** — At minimum every 90 days for production keys
2. **Use scoped tokens** — Grant minimum necessary permissions
3. **Use test keys for development** — Never use production keys in test environments
4. **Enable secret scanning** — GitHub secret scanning is enabled on all BlackRoad repos
5. **Audit access** — Review who has access to organization secrets periodically
6. **Use environment protection rules** — Require approval for production deployments
7. **Never log secrets** — Ensure CI/CD workflows mask secret values in logs

---

## 🔄 Key Rotation Schedule

| Secret | Rotation Frequency | Next Rotation |
|--------|--------------------|---------------|
| Stripe Keys | Every 90 days | Set after initial configuration |
| Google Service Account | Every 180 days | Set after initial configuration |
| Cloudflare API Token | Every 90 days | Set after initial configuration |
| Salesforce OAuth | Every 90 days | Set after initial configuration |
| Tailscale Auth Key | On expiry or 90 days | Set after initial configuration |
| Railway Token | Every 90 days | Set after initial configuration |
| HuggingFace Token | Every 180 days | Set after initial configuration |
| NPM Token | Every 90 days | Set after initial configuration |

---

## ✅ Verification

After adding secrets, verify they work by running the relevant workflows:

```bash
# Trigger a workflow manually to test
gh workflow run ci.yml
gh workflow run gdrive-backup.yml
gh workflow run deploy-cloudflare-workers.yml
```

Check the Actions tab for green status on all workflows.

---

*© 2024-2026 BlackRoad OS, Inc. All Rights Reserved.*
