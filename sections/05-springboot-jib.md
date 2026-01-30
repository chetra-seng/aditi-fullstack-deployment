---
layout: center
---

# Spring Boot + Jib

Deploy to Railway

---

# What is Jib?

<div class="grid grid-cols-2 gap-8">

<div>

## Key Benefits

- **Containerize Java apps without Docker**
- **No Dockerfile needed**
- **No Docker daemon required**
- **Builds optimized, layered images**
- **Maven/Gradle plugins**

</div>

<div>

## Traditional vs Jib

```
Traditional:
Source -> Build JAR -> Dockerfile
      -> docker build -> docker push

With Jib:
Source -> mvn jib:build -> Done!
```

</div>

</div>

---

# Jib Maven Configuration

```xml
<plugin>
    <groupId>com.google.cloud.tools</groupId>
    <artifactId>jib-maven-plugin</artifactId>
    <version>3.4.0</version>
    <configuration>
        <from>
            <image>eclipse-temurin:21-jre-alpine</image>
        </from>
        <to>
            <image>ghcr.io/${github.username}/${project.artifactId}</image>
            <tags>
                <tag>latest</tag>
                <tag>${project.version}</tag>
            </tags>
        </to>
        <container>
            <ports><port>8080</port></ports>
        </container>
    </configuration>
</plugin>
```

---

# Jib Gradle Configuration

```kotlin
// build.gradle.kts
plugins {
    id("com.google.cloud.tools.jib") version "3.4.0"
}

jib {
    from {
        image = "eclipse-temurin:21-jre-alpine"
    }
    to {
        image = "ghcr.io/username/myapp"
        tags = setOf("latest", version.toString())
    }
    container {
        ports = listOf("8080")
        environment = mapOf("SPRING_PROFILES_ACTIVE" to "prod")
    }
}
```

---

# Jib Commands

```bash
# Maven
mvn compile jib:build          # Build and push to registry
mvn compile jib:dockerBuild    # Build to local Docker daemon

# Gradle
./gradlew jib                  # Build and push to registry
./gradlew jibDockerBuild       # Build to local Docker daemon
```

<div class="mt-4 p-3 bg-green-500 bg-opacity-10 rounded">

**Tip:** Use `jib:dockerBuild` for local testing before pushing to registry

</div>

---

# What is Railway?

<div class="grid grid-cols-2 gap-8">

<div>

## Key Features

- **PaaS** - no server management
- **Auto SSL & networking**
- **Deploy from Docker images**
- **Built-in databases** (Postgres, Redis, etc.)
- **Environment variables UI**
- **Free tier available**

</div>

<div>

## Why Railway?

- No need to manage servers
- No Nginx/reverse proxy setup
- No manual SSL certificates
- Simple environment config
- Automatic deployments

</div>

</div>

---

# Railway Setup

<div class="grid grid-cols-2 gap-6">

<div>

## 1. Create Project

1. Sign up at [railway.app](https://railway.app)
2. Create new project
3. Select "Deploy from Docker Image"
4. Enter GHCR image URL:
   ```
   ghcr.io/username/myapp:latest
   ```

</div>

<div>

## 2. Configure

1. Add environment variables:
   - `SPRING_PROFILES_ACTIVE=prod`
   - `DATABASE_URL=...`
2. Railway auto-detects port 8080
3. Generate domain or add custom domain

</div>

</div>

---

# Railway with Database

<div class="grid grid-cols-2 gap-6">

<div>

## Add PostgreSQL

1. Click **+ New** in project
2. Select **Database → PostgreSQL**
3. Railway creates DB instantly
4. Copy connection string

</div>

<div>

## Connect to App

Add variable to your service:

```
DATABASE_URL=${{Postgres.DATABASE_URL}}
```

Railway auto-injects the connection string from your Postgres service.

</div>

</div>

---

# GitHub Actions: Build with Jib

```yaml
name: Build and Push
on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
      - name: Build and Push with Jib
        run: mvn compile jib:build
        env:
          JIB_TO_AUTH_USERNAME: ${{ github.actor }}
          JIB_TO_AUTH_PASSWORD: ${{ secrets.GITHUB_TOKEN }}
```

---

# Railway Auto-Deploy

<div class="grid grid-cols-2 gap-8">

<div>

## Option 1: Webhook

1. In Railway, go to **Settings → Deploy**
2. Copy the **Deploy Webhook URL**
3. Add to GitHub Actions:

```yaml
- name: Trigger Railway Deploy
  run: curl -X POST ${{ secrets.RAILWAY_WEBHOOK }}
```

</div>

<div>

## Option 2: Railway CLI

```yaml
- name: Install Railway CLI
  run: npm i -g @railway/cli

- name: Deploy to Railway
  run: railway up
  env:
    RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
```

</div>

</div>

---

# Full CI/CD Pipeline

```yaml
name: Build and Deploy
on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
      - run: mvn compile jib:build
        env:
          JIB_TO_AUTH_USERNAME: ${{ github.actor }}
          JIB_TO_AUTH_PASSWORD: ${{ secrets.GITHUB_TOKEN }}
      - run: curl -X POST ${{ secrets.RAILWAY_WEBHOOK }}
```

---
layout: center
---

# Homework: No Excuses

<div class="text-left text-lg">

Your mission (you MUST accept it):

1. **Frontend:** Add GitHub Actions workflow to deploy to Vercel
2. **Backend:** Add GitHub Actions workflow to build & push with Jib to GHCR

**Bonus (for the brave):** Add CD workflow to auto-deploy backend

</div>

<div class="mt-4 p-4 bg-blue-500 bg-opacity-20 rounded">

**Note:** Don't worry about frontend ↔ backend integration yet. Just make sure:
- Frontend is deployed and accessible
- Backend is connected to DB and accessible

</div>

<div class="mt-4 p-4 bg-red-500 bg-opacity-20 rounded text-center">

⚠️ No workflow = No mercy at next class ⚠️

</div>

