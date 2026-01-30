---
layout: center
---

# Summary

Bringing it all together

---

# What We Covered

| Topic | Key Takeaways |
|-------|---------------|
| **Docker** | Containers, images, Dockerfile, multi-stage builds |
| **Docker Compose** | Multi-container apps, volumes, networks, environment |
| **GitHub Actions** | CI/CD workflows, triggers, secrets, Docker build/push |
| **Vercel** | Frontend deployment, preview deploys, environment config |
| **Spring Boot + Jib** | Containerize Java apps without Docker, deploy to Railway |

---

# Deployment Architecture

```
+--------------------------------------------------------------+
|                        GitHub                                 |
|  +-----------+    +-----------------------------+             |
|  |   Push    |--->|     GitHub Actions          |             |
|  +-----------+    |  +-------+  +------------+  |             |
|                   |  | Lint  |  | Build+Push |  |             |
|                   |  +-------+  +------------+  |             |
|                   +-------------+---------------+             |
+-----------------------------+--------------------------------+
                              |
           +------------------+------------------+
           |                                     |
           v                                     v
   +---------------+                  +------------------+
   |    Vercel     |                  |     Railway      |
   |  (Frontend)   |                  |    (Backend)     |
   |               |<---- API ------->|                  |
   |  Next.js      |                  |  Spring Boot     |
   |  Auto SSL     |                  |  PostgreSQL      |
   |  Edge CDN     |                  |  Auto SSL        |
   +---------------+                  +------------------+
```

---

# CI/CD Flow

<div class="grid grid-cols-2 gap-8">

<div>

## Frontend (Vercel)

1. Push to GitHub
2. Vercel auto-detects
3. Builds & deploys
4. Preview URL for PRs
5. Production on merge

</div>

<div>

## Backend (Railway)

1. Push to GitHub
2. GitHub Actions triggers
3. Jib builds container
4. Push to GHCR
5. Webhook triggers Railway
6. Railway pulls & deploys

</div>

</div>

---

# Key Takeaways

<div class="grid grid-cols-2 gap-4">

<div class="p-4 bg-blue-500 bg-opacity-10 rounded">

### Docker

- Containers = portable environments
- Multi-stage builds = smaller images
- Docker Compose = local development

</div>

<div class="p-4 bg-green-500 bg-opacity-10 rounded">

### CI/CD

- Automate everything
- Lint & test before deploy
- Use secrets for credentials

</div>

<div class="p-4 bg-purple-500 bg-opacity-10 rounded">

### Frontend (Vercel)

- Zero-config deploys
- Preview deployments for PRs
- Edge network = fast globally

</div>

<div class="p-4 bg-yellow-500 bg-opacity-10 rounded">

### Backend (Railway)

- Jib = no Dockerfile needed
- GHCR = free container registry
- Railway = no server management

</div>

</div>

---

# Best Practices Checklist

<div class="grid grid-cols-2 gap-6">

<div>

### Security

- [ ] Use secrets, never hardcode
- [ ] HTTPS everywhere
- [ ] Non-root containers
- [ ] Regular dependency updates

</div>

<div>

### Reliability

- [ ] Health checks
- [ ] Auto-restart policies
- [ ] Database backups
- [ ] Monitoring/logging

</div>

<div>

### Performance

- [ ] Multi-stage builds
- [ ] Layer caching
- [ ] CDN for static assets
- [ ] Connection pooling

</div>

<div>

### Development

- [ ] Local Docker Compose
- [ ] Preview deployments
- [ ] Branch protection
- [ ] Code review process

</div>

</div>

