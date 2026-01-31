---
layout: center
---

# Docker Basics

Understanding containerization fundamentals

---

# What is Docker?

<div class="grid grid-cols-2 gap-8">

<div>

## Key Benefits

- **Containerization platform** - packages apps with their dependencies
- **Lightweight** - shares host OS kernel (unlike VMs)
- **Portable** - runs the same everywhere
- **Isolated** - each container has its own filesystem, network, processes

</div>

<div>

## Architecture

<div class="flex flex-col gap-1 text-xs">
  <div class="p-2 border-2 border-blue-400 rounded text-center font-bold bg-blue-500 bg-opacity-10">
    Your Application
  </div>
  <div class="text-center text-slate-400">↓</div>
  <div class="p-2 border-2 border-purple-400 rounded text-center font-bold bg-purple-500 bg-opacity-10">
    Dependencies + Runtime + Config
  </div>
  <div class="text-center text-slate-400">↓</div>
  <div class="p-2 border-2 border-amber-400 rounded text-center font-bold bg-amber-500 bg-opacity-10">
    Docker Engine
  </div>
  <div class="text-center text-slate-400">↓</div>
  <div class="p-2 border-2 border-green-400 rounded text-center font-bold bg-green-500 bg-opacity-10">
    Host OS
  </div>
</div>

</div>

</div>

---

# Docker vs Virtual Machines

<div class="grid grid-cols-2 gap-8">
<div>

## Virtual Machines

<div class="flex flex-col gap-1 text-xs">
  <div class="flex gap-1">
    <div class="flex-1 p-1.5 border-2 border-blue-400 rounded text-center bg-blue-500 bg-opacity-10">App A</div>
    <div class="flex-1 p-1.5 border-2 border-blue-400 rounded text-center bg-blue-500 bg-opacity-10">App B</div>
  </div>
  <div class="flex gap-1">
    <div class="flex-1 p-1.5 border-2 border-red-400 rounded text-center bg-red-500 bg-opacity-10">Guest OS</div>
    <div class="flex-1 p-1.5 border-2 border-red-400 rounded text-center bg-red-500 bg-opacity-10">Guest OS</div>
  </div>
  <div class="p-1.5 border-2 border-purple-400 rounded text-center bg-purple-500 bg-opacity-10">Hypervisor</div>
  <div class="p-1.5 border-2 border-slate-400 rounded text-center bg-slate-500 bg-opacity-10">Host OS</div>
</div>

- Heavy (GBs)
- Slow startup (minutes)
- Full OS isolation

</div>
<div>

## Containers

<div class="flex flex-col gap-1 text-xs">
  <div class="flex gap-1">
    <div class="flex-1 p-1.5 border-2 border-blue-400 rounded text-center bg-blue-500 bg-opacity-10">App A</div>
    <div class="flex-1 p-1.5 border-2 border-blue-400 rounded text-center bg-blue-500 bg-opacity-10">App B</div>
  </div>
  <div class="p-1.5 border-2 border-amber-400 rounded text-center bg-amber-500 bg-opacity-10">Docker Engine</div>
  <div class="p-1.5 border-2 border-green-400 rounded text-center bg-green-500 bg-opacity-10">Host OS</div>
</div>

- Lightweight (MBs)
- Fast startup (seconds)
- Process isolation

</div>
</div>

---

# Real-World Comparison: Ubuntu

| Resource | Ubuntu VM | Ubuntu Container |
|----------|-----------|------------------|
| **Image Size** | ~2.5 GB (ISO) | ~78 MB (`ubuntu:24.04`) |
| **Disk After Install** | ~5-10 GB | ~78 MB |
| **RAM (idle)** | ~500 MB - 1 GB | ~5-10 MB |
| **Boot Time** | 30-60 seconds | < 1 second |
| **Processes Running** | 100+ (systemd, services) | 1 (your app) |

<div class="grid grid-cols-2 gap-4 mt-4">

<div class="p-3 bg-red-500 bg-opacity-10 rounded text-sm">

**VM includes:** Full kernel, systemd, networking stack, package manager, logging, cron, etc.

</div>

<div class="p-3 bg-green-500 bg-opacity-10 rounded text-sm">

**Container includes:** Minimal filesystem, libc, core utilities. Shares host kernel.

</div>

</div>

---

# Key Docker Concepts

| Concept | Description |
|---------|-------------|
| **Image** | Read-only template with instructions for creating a container |
| **Container** | Running instance of an image |
| **Dockerfile** | Text file with instructions to build an image |
| **Registry** | Storage for Docker images (Docker Hub, GitHub Container Registry) |
| **Volume** | Persistent data storage for containers |
| **Network** | Communication between containers |

---

# Essential Docker Commands

```bash
# Images
docker pull nginx                    # Download image
docker images                        # List images
docker rmi nginx                     # Remove image

# Containers
docker run -d -p 80:80 nginx         # Run container (detached, port mapping)
docker ps                            # List running containers
docker ps -a                         # List all containers
docker stop <container_id>           # Stop container
docker rm <container_id>             # Remove container

# Logs & Debugging
docker logs <container_id>           # View logs
docker exec -it <container_id> bash  # Execute command in container

# Cleanup
docker system prune -a               # Remove all unused resources
```

---

# Dockerfile Basics

```dockerfile
# Base image
FROM node:20-alpine

# Set working directory
WORKDIR /app

# Copy dependency files first (layer caching)
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application code
COPY . .

# Expose port
EXPOSE 3000

# Start command
CMD ["node", "index.js"]
```

---

# Dockerfile Best Practices

<div class="grid grid-cols-2 gap-6">

<div class="p-4 bg-green-500 bg-opacity-10 rounded">

### Do

- Use specific base image tags (`node:20-alpine`)
- Layer caching - put frequently changing layers last
- Use `.dockerignore` to exclude unnecessary files
- Run as non-root user
- One process per container

</div>

<div class="p-4 bg-red-500 bg-opacity-10 rounded">

### Don't

- Use `latest` tag in production
- Install unnecessary packages
- Store secrets in images
- Run as root in production
- Ignore layer order

</div>

</div>

---

# Multi-stage Build Example

```dockerfile
# Build stage
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Production stage
FROM node:20-alpine AS production
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
EXPOSE 3000
USER node
CMD ["node", "dist/index.js"]
```

<div class="mt-4 p-3 bg-blue-500 bg-opacity-10 rounded">

**Benefits:** Build tools not in final image, smaller size, better security

</div>

---

# .dockerignore

```plaintext
node_modules
npm-debug.log
.git
.gitignore
.env
.env.local
Dockerfile
docker-compose.yml
.dockerignore
README.md
.vscode
coverage
.nyc_output
```

<div class="mt-4 p-3 bg-yellow-500 bg-opacity-10 rounded">

**Tip:** Always exclude `node_modules` - dependencies should be installed inside the container

</div>

---
layout: center
---

# Practice: Docker Basics

<div class="text-left">

1. Create a simple Node.js app (`index.js`)
2. Write a `Dockerfile`
3. Build the image: `docker build -t my-app .`
4. Run the container: `docker run -p 3000:3000 my-app`
5. Test it: `curl http://localhost:3000`

</div>

