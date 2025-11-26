Here are **the exact, clean steps** to pull
`mongodb/mongodb-community-server:8.0.11-ubi9`
and push it into your **internal enterprise Docker/OCI registry** (Artifactory, Harbor, Nexus, Azure Container Registry, AWS ECR, Red Hat Quay, OpenShift, etc.).

Works with **Docker** or **Podman**.

---

# ✅ **Step 1 — Pull the image from MongoDB’s public registry**

MongoDB publishes the image on Docker Hub.

```bash
podman pull docker.io/mongodb/mongodb-community-server:8.0.11-ubi9
# OR Docker:
docker pull mongodb/mongodb-community-server:8.0.11-ubi9
```

Confirm you retrieved it:

```bash
podman images | grep mongodb
```

---

# ✅ **Step 2 — Log in to your internal registry**

Examples:

### ❗ Replace registry URL:

```
registry.mycompany.com
registry.company.local:5000
internal-registry:8443
```

### Podman:

```bash
podman login registry.mycompany.com
```

### Docker:

```bash
docker login registry.mycompany.com
```

---

# ✅ **Step 3 — Tag the image for the internal registry**

```bash
podman tag \
  mongodb/mongodb-community-server:8.0.11-ubi9 \
  registry.mycompany.com/mongodb/mongodb-community-server:8.0.11-ubi9
```

If your registry requires a namespace:

```bash
podman tag \
  mongodb/mongodb-community-server:8.0.11-ubi9 \
  registry.mycompany.com/myteam/mongodb-community-server:8.0.11-ubi9
```

---

# ✅ **Step 4 — Push the image to the internal registry**

```bash
podman push registry.mycompany.com/mongodb/mongodb-community-server:8.0.11-ubi9
```

Docker equivalent:

```bash
docker push registry.mycompany.com/mongodb/mongodb-community-server:8.0.11-ubi9
```

---

# 🚀 You're done

Now the image is available inside your company’s enterprise registry.

Example pull for other teams:

```bash
podman pull registry.mycompany.com/mongodb/mongodb-community-server:8.0.11-ubi9
```

---

# 🔒 **If the image cannot be pulled from the internet (air-gapped environments)**

If your RHEL/enterprise environment is **air gapped**, you have two options:

### **Option A — Save + transfer image manually**

1. On a machine with internet:

```bash
podman pull mongodb/mongodb-community-server:8.0.11-ubi9
podman save -o mongo-8.0.11-ubi9.tar mongodb/mongodb-community-server:8.0.11-ubi9
```

2. Transfer `mongo-8.0.11-ubi9.tar` via USB/share/internal SCP.

3. In air-gapped environment:

```bash
podman load -i mongo-8.0.11-ubi9.tar
podman tag mongodb/mongodb-community-server:8.0.11-ubi9 registry.mycompany.com/mongodb/mongo:8.0.11-ubi9
podman push registry.mycompany.com/mongodb/mongo:8.0.11-ubi9
```

---

# 🧱 **Option B — Build your own UBI-based MongoDB image and push it**

If your company does **not allow pulling images from Docker Hub**, then you must **build your own image** using UBI from Red Hat and MongoDB RPMs.

I can give you a ready-to-use **RHEL/UBI-compliant Dockerfile**.

---

# ❓ Want me to generate an enterprise-approved UBI Dockerfile for:

* MongoDB 8.0.11
* MongoDB 7.0
* MongoDB 6.0 (Cosmos vCore equivalent)

Just tell me which version you want to standardize internally.
