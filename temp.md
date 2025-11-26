Here are the **exact steps** to migrate
`mongodb/mongodb-community-server:8.0.11-ubi9`
to **quay.io** (public Quay) or your **enterprise Quay** instance.

Works with **Docker** or **Podman**.

---

# ✅ 1. Pull the original MongoDB UBI image

```bash
podman pull docker.io/mongodb/mongodb-community-server:8.0.11-ubi9
```

Check:

```bash
podman images | grep mongodb
```

---

# ✅ 2. Log into Quay

### If you are using **quay.io**:

```bash
podman login quay.io
```

### If you are using **enterprise Quay**:

```bash
podman login quay.mycompany.com
```

You need **username & password** or **robot account**.

---

# ✅ 3. Tag the image for Quay

Quay requires namespace:

Pattern:

```
quay.io/<username-or-org>/<repo-name>:tag
```

Example:

```bash
podman tag \
  mongodb/mongodb-community-server:8.0.11-ubi9 \
  quay.io/myorg/mongodb-community-server:8.0.11-ubi9
```

For enterprise Quay:

```bash
podman tag \
  mongodb/mongodb-community-server:8.0.11-ubi9 \
  quay.mycompany.com/platform/mongodb-community-server:8.0.11-ubi9
```

---

# ✅ 4. Push to Quay

### Quay.io push:

```bash
podman push quay.io/myorg/mongodb-community-server:8.0.11-ubi9
```

### Enterprise Quay:

```bash
podman push quay.mycompany.com/platform/mongodb-community-server:8.0.11-ubi9
```

---

# 🎉 Done

Now your image is in Quay and can be pulled:

```bash
podman pull quay.io/myorg/mongodb-community-server:8.0.11-ubi9
```

---

# 🧊 Optional (air-gapped migration)

If your environment cannot pull directly from Docker Hub:

### On machine with internet:

```bash
podman pull docker.io/mongodb/mongodb-community-server:8.0.11-ubi9
podman save -o mongo.tar mongodb/mongodb-community-server:8.0.11-ubi9
```

Transfer `mongo.tar` to secure environment.

### In internal network:

```bash
podman load -i mongo.tar
podman tag mongodb/mongodb-community-server:8.0.11-ubi9 quay.mycompany.com/platform/mongodb-community-server:8.0.11-ubi9
podman push quay.mycompany.com/platform/mongodb-community-server:8.0.11-ubi9
```

---

# ❓ If you want, I can also provide:

### ✔ A fully enterprise-approved UBI-based MongoDB 8.0 image

### ✔ A script that auto-pulls → retags → pushes to Quay

### ✔ A Quay repository layout suggestion (org, teams, tags, retention)

Do you want me to generate any of these?
