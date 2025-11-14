Below is a clean, ready-to-use **documentation-style guide** you can paste into your internal wiki or README.
If you want this as a **proper canvas document**, tell me and I’ll generate it there.

---

# VS Code – Azure Cosmos DB for MongoDB (vCore)

## Installation & Authentication Guide

### Using `az login` + VS Code DocumentDB Extension

### (Entra ID Authentication – Role Already Granted)

---

## 1. **Prerequisites**

Before connecting via VS Code, ensure the following:

1. **Azure CLI is installed**

   ```bash
   az version
   ```

2. **You have an Entra ID role assigned** on the Cosmos DB for MongoDB vCore database or cluster.
   Required role examples:

   * `Cosmos DB Built-in Data Reader`
   * `Cosmos DB Built-in Data Contributor`
   * Or a custom role granting MongoDB permissions.

3. **VS Code installed**

4. **VS Code Extension: "Azure Cosmos DB"** (DocumentDB/MongoDB)

   * Publisher: Microsoft

---

## 2. **Install the VS Code Extension**

1. Open **VS Code**
2. Go to **Extensions** (`Ctrl + Shift + X`)
3. Search for:

   ```
   Azure Cosmos DB
   ```
4. Install the extension

This adds support for:

* MongoDB connections
* Cosmos DB vCore (via connection strings)
* Azure sign-in workflow

---

## 3. **Authenticate to Azure using Azure CLI**

The VS Code extension can reuse your Azure CLI login session.

### Step 1 — Login to Azure

```bash
az login
```

A browser window opens → select your Entra ID account.

### Step 2 — Select subscription (optional)

If you have multiple subscriptions:

```bash
az account set --subscription "<subscription-name-or-id>"
```

### Step 3 — Confirm Context

```bash
az account show
```

You are now authenticated in your CLI session.

---

## 4. **Get the Entra ID–based Connection String (MongoDB vCore)**

In the Azure Portal:

1. Navigate to **Azure Cosmos DB for MongoDB vCore**
2. Select **Connection Strings**
3. Choose **Entra ID Authentication**
4. Copy the connection string, which looks like:

```
mongodb://<cluster-host>:10255/?authMechanism=DEFAULT&authSource=%24external&ssl=true
```

or newer format:

```
mongodb+srv://<cluster-host>/?authMechanism=PLAIN&authSource=%24external&ssl=true
```

No password is embedded because authentication relies on your Entra ID token.

---

## 5. **Connect via VS Code Extension

(Using Entra ID Token After `az login`)**

### Method 1 — Quick Connect

1. In VS Code, open the **Azure** panel
2. Expand **Azure Cosmos DB**
3. Click **"Connect to a Database"**
4. Select **MongoDB**
5. Paste the **Entra ID connection string**
6. When asked for authentication → choose:

   * **Entra ID / Azure AD authentication token**
   * or **"Use Azure CLI authentication"** (automatically uses `az login` token)

If CLI is logged in, VS Code automatically retrieves and passes the token.

---

## 6. **How Authentication Works Internally**

### ✔ You logged in via `az login`

→ Your local device now holds an active Entra ID token.

### ✔ VS Code DocumentDB extension retrieves that token

→ It injects it during the MongoDB handshake.

### ✔ MongoDB vCore validates

* Your identity
* Your assigned role
* Cluster permissions

### ✔ Connection established without passwords

→ Secure token-based authentication.

---

## 7. **Verifying Role Assignment**

In Azure Portal:

1. Open your **Cosmos DB vCore** instance
2. Go to **Access Control (IAM)**
3. Verify your account has:

   * A Cosmos DB data role
   * At database, cluster, or subscription level

If role is missing, the login will fail even if `az login` succeeds.

---

## 8. **Troubleshooting**

### **Issue: VS Code error – authentication failed**

Check:

* You granted the correct **Cosmos DB Built-in Role**
* You used the connection string for **Entra ID**, not password-based
* SSL is enabled in the string (required)

### **Issue: CLI token not picked up**

Run:

```bash
az login --tenant <tenant-id>
```

Restart VS Code.

### **Issue: Using SRV connection string fails**

Try converting to non-SRV:

* Sometimes local firewall/proxy blocks `_mongodb._tcp` DNS lookups.
* Use the regular `mongodb://host:port` version.

---

## 9. **Example: Full Connection String for Entra ID**

```
mongodb://cosmos-demo.mongo.cosmos.azure.com:10255/?ssl=true&retryWrites=false&authSource=%24external&authMechanism=DEFAULT
```

VS Code will attach your Entra ID token automatically.

---

If you'd like, I can also generate:

✅ A printable PDF
✅ A version formatted as Markdown for GitHub
✅ Architecture diagrams
✅ A step-by-step screenshot guide

Just tell me the format you want.
