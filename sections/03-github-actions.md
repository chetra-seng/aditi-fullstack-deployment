---
layout: center
---

# GitHub Actions

CI/CD Automation

---

# What is CI/CD?

<div class="grid grid-cols-2 gap-8">

<div>

## CI - Continuous Integration

Automatically **build and test** code every time changes are pushed.

- Run linters & formatters
- Run unit/integration tests
- Build the application
- Catch bugs early

</div>

<div>

## CD - Continuous Deployment

Automatically **deploy** code to production after CI passes.

- Build Docker images
- Push to container registry
- Deploy to servers/cloud
- Zero manual intervention

</div>

</div>

---

# What is GitHub Actions?

<div class="grid grid-cols-2 gap-8">

<div>

## Key Features

- **CI/CD platform** built into GitHub
- **Event-driven** - triggers on push, PR, schedule, etc.
- **YAML workflow files** in `.github/workflows/`
- **Free tier** - 2,000 mins/month for private repos

</div>

<div>

## Workflow Overview

```
Push to main -> Run tests -> Build -> Deploy
     |              |         |        |
   Event         Job 1     Job 2    Job 3
```

</div>

</div>

---

# CI/CD Flow

```
Pull Request                          Push to Main
     │                                      │
     ▼                                      ▼
┌─────────┐                          ┌─────────────┐
│  Lint   │──┐                       │ Build Image │
└─────────┘  │                       └──────┬──────┘
             │  All pass?                   │
┌─────────┐  ├────────────► Merge ──────────┤
│  Test   │──┤                              ▼
└─────────┘  │                       ┌─────────────┐
             │                       │ Push to     │
┌─────────┐  │                       │ Registry    │
│  Build  │──┘                       └──────┬──────┘
└─────────┘                                 │
                                            ▼
                                     ┌─────────────┐
                                     │ Deploy to   │
                                     │ Server      │
                                     └─────────────┘
```

---

# Workflow Structure

```yaml
name: CI/CD Pipeline               # Workflow name

on:                                # Triggers
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:                              # Jobs to run
  test:                            # Job name
    runs-on: ubuntu-latest         # Runner OS

    steps:                         # Sequential steps
      - uses: actions/checkout@v4  # Use an action

      - name: Setup Node.js        # Step name
        uses: actions/setup-node@v4
        with:
          node-version: '20'

      - name: Install & Test
        run: |                     # Run shell commands
          npm ci
          npm test
```

---

# Common Triggers

```yaml
on:
  # Push to specific branches
  push:
    branches: [main, develop]
    paths:
      - 'src/**'
      - 'package.json'

  # Pull requests
  pull_request:
    branches: [main]

  # Manual trigger
  workflow_dispatch:
    inputs:
      environment:
        description: 'Deploy environment'
        required: true
        default: 'staging'

  # Scheduled (cron)
  schedule:
    - cron: '0 0 * * *'  # Daily at midnight
```

---

# Popular Actions

```yaml
steps:
  # Checkout code
  - uses: actions/checkout@v4

  # Setup runtimes
  - uses: actions/setup-node@v4
  - uses: actions/setup-java@v4
  - uses: actions/setup-python@v5

  # Caching
  - uses: actions/cache@v4
    with:
      path: ~/.npm
      key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}

  # Docker
  - uses: docker/setup-buildx-action@v3
  - uses: docker/login-action@v3
  - uses: docker/build-push-action@v5
```

---

# Secrets & Environment Variables

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production        # Use GitHub environment

    steps:
      - name: Deploy
        env:
          API_KEY: ${{ secrets.API_KEY }}           # Repository secret
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
        run: |
          echo "Deploying with secrets..."
```

<div class="mt-4 p-3 bg-yellow-500 bg-opacity-10 rounded">

**Setting secrets:** Repository -> Settings -> Secrets and variables -> Actions -> New repository secret

</div>

---

# Common Use Cases

| Use Case | Trigger | What it does |
|----------|---------|--------------|
| **Lint & Format** | PR | Check code style (ESLint, Prettier) |
| **Tests** | PR + Push | Run unit/integration tests |
| **Build** | PR | Verify code compiles |
| **Security Scan** | Scheduled | Check vulnerabilities |
| **Docker Build** | Push to main | Build & push image |
| **Deploy** | Push to main | Deploy to server |

---

# Example: CI Pipeline

```yaml
name: CI
on:
  pull_request:
    branches: [main]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm run lint

  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm test

  build:
    needs: [lint, test]    # Wait for lint & test to pass
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm ci && npm run build
```

---

# Example: Build & Push to Docker Hub

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_TOKEN }}
      - uses: docker/build-push-action@v5
        with:
          push: true
          tags: user/app:${{ github.sha }}
```

---

# Example: Build & Push to GHCR

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@v4
      - uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - uses: docker/build-push-action@v5
        with:
          push: true
          tags: ghcr.io/${{ github.repository }}:latest
```

---

# Example: CD Pipeline

```yaml
name: Deploy
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: |
          echo ${{ secrets.GITHUB_TOKEN }} | docker login ghcr.io -u ${{ github.actor }} --password-stdin
          docker build -t ghcr.io/${{ github.repository }}:latest .
          docker push ghcr.io/${{ github.repository }}:latest
      - uses: appleboy/ssh-action@v1
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SSH_PRIVATE_KEY }}
          script: docker pull ghcr.io/${{ github.repository }}:latest && docker compose up -d
```

---
layout: center
---

# Practice: CI for Next.js

<div class="text-left">

1. Create `.github/workflows/ci.yml`
2. Trigger on pull requests to `main`
3. Add steps to:
   - Checkout code
   - Setup Node.js
   - Install dependencies
   - Run format check (`npm run format:check`)
   - Run lint (`npm run lint`)
4. Push and open a PR to test

</div>

