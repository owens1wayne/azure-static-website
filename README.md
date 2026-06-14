# azure-static-website

![Watch Me Work] (https://loom.com/share/dec13f042471437f96dc0e6f0df61804)

Deployed a public-facing static website on Azure Blob Storage using serverless PaaS hosting — no VMs, no web servers, no infrastructure to manage. Covers resource group creation, storage account configuration, static website enablement, and HTTPS endpoint deployment on Azure.

# Lab 01: Hosting Your First Static Website on Azure Blob Storage

![Azure](https://img.shields.io/badge/Azure-Storage-0078D4?style=flat&logo=microsoftazure&logoColor=white)
![Hosting](https://img.shields.io/badge/Hosting-Serverless-blueviolet)
![Deployed Via](https://img.shields.io/badge/Deployed%20Via-Azure%20Portal-blue)

---

## Overview

Every cloud security engineer needs to understand how things are *built* before they can secure them. This project is where that journey starts.

In this project, I deployed a publicly accessible static website on Azure — no virtual machines, no web servers, no OS to patch. Just Azure Blob Storage, a `$web` container, and an HTML file. That's the beauty of PaaS and serverless hosting: you focus on the outcome, and Azure handles the infrastructure underneath.

Simple? Yes. But don't let that fool you. Misconfigured storage accounts are one of the most common causes of data exposure in Azure environments. Understanding how this works from the ground up is exactly what makes the difference between an engineer who can *use* the cloud and one who can *secure* it.

> 💡 *By the end of this project, I was already asking the right questions: Who else can hit this URL? What happens if someone uploads a malicious file to this container? Those questions matter.*

---

## Architecture

```
User (Browser)
│
│   HTTPS Request
│
▼
Azure Storage Account (stlab01[yourname])
│   Region: East US | Performance: Standard | Redundancy: LRS
│
│   Static Website Hosting Enabled
│   Primary Endpoint: https://stlab01[yourname].z13.web.core.windows.net
│
▼
$web Container  (auto-created by Azure)
│
├── index.html  ◄─── Served to the user
└── 404.html    ◄─── Served on error (optional)
```

---

## Azure Services Used

| Service | Purpose | Tier Used |
|---|---|---|
| **Azure Resource Groups** | Logical container to organize all lab resources | N/A |
| **Azure Blob Storage** | Stores and serves the static website files | Standard |
| **Static Website Hosting** | Enables public HTTPS serving directly from Blob Storage | Built-in feature |

---

## Prerequisites

- [ ] An active Azure Subscription (Free Tier is all you need)

- [ ] A text editor — [VS Code](https://code.visualstudio.com/) is recommended, Notepad works too
- [ ] Access to the [Azure Portal](https://portal.azure.com)

---

## Lab Variables

Stick to these naming conventions throughout the lab. Consistent naming is a real-world habit that makes resource management and cleanup much easier — especially when you're working across multiple environments.

| Variable | Value |
|---|---|
| **Resource Group** | `rg-lab01-[yourname]` |
| **Location** | `East US` |
| **Storage Account Name** | `stlab01[yourname]` |

> ⚠️ **Storage Account Naming Rules:** Globally unique, all lowercase, letters and numbers only, 3–24 characters. Example: `stlab01jhante`

---

## Deployment Walkthrough

### Phase 1 — Create the Resource Group

Think of a Resource Group as a folder for everything you're about to build. Creating one first is best practice — it keeps things organized and makes teardown a single step instead of hunting down individual resources.

1. Log in to the [Azure Portal](https://portal.azure.com).
2. Search for **Resource Groups** in the top search bar and select it.
3. Click **+ Create** and fill in the Basics tab:
   - **Subscription:** Select your subscription
   - **Resource Group:** `rg-lab01-[yourname]`
   - **Region:** `(US) East US`
4. Click **Review + create** → **Create**.

---

### Phase 2 — Create the Storage Account

This is the core resource for the project. The Storage Account is what holds your files and, once configured, serves them to the internet.

1. Search for **Storage accounts** in the top search bar and select it.
2. Click **+ Create** and fill in the Basics tab:

   | Field | Value |
   |---|---|
   | Resource Group | `rg-lab01-[yourname]` |
   | Storage account name | `stlab01[yourname]` |
   | Region | `(US) East US` |
   | Performance | `Standard` |
   | Redundancy | `Locally-redundant storage (LRS)` |

   > 💡 *LRS is the most affordable redundancy option and is perfect for labs. In a production environment, you'd typically step up to GRS (Geo-redundant) or GZRS for anything business-critical — good to know for interviews.*

3. Click **Review + create**, wait for validation to pass, then click **Create**.
4. Deployment takes about 30 seconds. Click **Go to resource** when it finishes.

---

### Phase 3 — Enable Static Website Hosting

This is the step that flips the switch — turning a regular Storage Account into a web host.

1. On the left-hand menu of your Storage Account, find the **Data management** section.
2. Click **Static website**.
3. Toggle **Static website** from **Disabled** to **Enabled**.
4. Fill in the fields:
   - **Index document name:** `index.html`
   - **Error document path:** `404.html` *(optional, but worth doing)*
5. Click **Save**.

> 🔴 **Don't skip this:** After saving, a **Primary endpoint** URL will appear — something like `https://stlab01jhante.z13.web.core.windows.net/`. Copy it and keep it handy. This is your site's live address.

---

### Phase 4 — Build the Website File

Nothing fancy here — just enough HTML to prove the concept works.

1. Open your text editor and paste the following:

```html
<!DOCTYPE html>
<html>
  <head>
    <title>My First Cloud Site</title>
    <style>
      body {
        font-family: sans-serif;
        text-align: center;
        margin-top: 50px;
        background-color: #f0f0f0;
      }
      h1 { color: #0078d4; }
    </style>
  </head>
  <body>
    <h1>Hello from the Cloud!</h1>
    <p>This site is hosted on Azure Blob Storage.</p>
    <p>Deployed by: [Your Name]</p>
  </body>
</html>
```

2. Replace `[Your Name]` with your name.
3. Save the file to your Desktop as **`index.html`**.

> ⚠️ *Windows users — double-check that the file saved as `index.html` and not `index.html.txt`. You may need to enable "Show file extensions" in File Explorer to confirm. This small detail trips people up more than you'd expect.*

---

### Phase 5 — Upload Your Content

1. Head back to the Azure Portal on your Storage Account page.
2. On the left menu, click **Containers** under **Data storage**.
3. You'll see a container called **`$web`** — Azure created this automatically when you enabled static website hosting. Click to open it.
4. Click **Upload**, browse to your Desktop, and select `index.html`.
5. Click **Upload**.

---

### Phase 6 — Validate the Deployment

1. Open a new browser tab.
2. Paste in the **Primary endpoint** URL from Phase 3.
3. Hit **Enter**.

You should see your "Hello from the Cloud!" page load live in the browser.

**That's a real, publicly accessible website — deployed on Azure with no servers, no runtime, and no infrastructure to manage.** Not bad for a first deployment. 

---

## Troubleshooting

### Getting a 404?

Work through this checklist before anything else:

- [ ] Did you upload `index.html` to the **`$web` container** specifically? It's easy to accidentally upload to a different container.
- [ ] Is the filename exactly `index.html`? No typos, no hidden `.txt` extension.
- [ ] Is the **Index document name** set to `index.html` in your Static website settings? Go back and verify.
- [ ] Are you using the **Primary endpoint URL** (the `*.web.core.windows.net` one)? The regular blob storage URL is different and won't serve the site.

---

## Security Considerations

*This is the section that turns a basic Project into a security engineer's Deployment.*

Getting something working is step one. Understanding the security implications of what you just built is what actually matters. Here's what every cloud security engineer should recognize about this setup:

**The `$web` container is publicly readable by design.** Anyone with the URL can access any file uploaded there. That's the point for a static website — but it means you should never upload anything sensitive to this container. Ever.

**There's no authentication on this endpoint.** Public means anyone, anywhere, no credentials required. In future labs, we'll look at how to put Azure Front Door or Azure Static Web Apps in front of storage to add auth, WAF protection, and more.

**Overly permissive storage accounts are one of the most common misconfigurations in Azure.** Microsoft Defender for Cloud flags these automatically, and they consistently appear in real-world cloud security assessments. Knowing how they're created is the first step to knowing how to find and fix them.

**In production, consider enabling [Microsoft Defender for Storage](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-storage-introduction).** It detects anomalous access patterns, malware uploads, and data exfiltration attempts — automatically.

---

## Cleanup / Teardown

Always clean up after any project. It's a professional habit, and it protects your Azure credits.

**Option 1 — Azure Portal:**
1. Go to **Resource Groups** and open `rg-lab01-[yourname]`.
2. Click **Delete resource group**, type the name to confirm, and click **Delete**.

**Option 2 — Azure CLI:**
```bash
az group delete --name rg-lab01-[yourname] --yes --no-wait
```

> 💡 *`--no-wait` returns control to your terminal immediately while Azure handles deletion in the background. `--yes` skips the confirmation prompt. Two flags that save you ten seconds every time.*

---

## Take aways

- How to use **Azure Resource Groups** to organize and manage cloud resources efficiently
- How **Azure Blob Storage** can function as a fully serverless web host using the Static Website feature
- The difference between a blob storage URL and a static website primary endpoint — and why that distinction matters
- Why publicly accessible storage containers carry real security implications that engineers need to understand from day one
- How to think about cloud deployments not just as *working* but as *secure* — even at the simplest level

---

