---
layout: center
---

# Vercel Deployment

Frontend deployment made easy

---

# What is Vercel?

<div class="grid grid-cols-2 gap-8">

<div>

## Key Features

- **Platform for frontend deployment**
- **Zero-config** for most frameworks
- **Git integration** - auto deploy on push
- **Edge network** - global CDN
- **Serverless functions** - API routes
- **Preview deployments** - for every PR

</div>

<div>

## Supported Frameworks

- Next.js (by Vercel)
- React / Vite
- Vue / Nuxt
- Svelte / SvelteKit
- Angular
- Static sites

</div>

</div>

---

# Deployment Options

## 1. Via Dashboard (Easiest)

<div class="grid grid-cols-2 gap-8 mt-4">

<div>

**Steps:**
1. Go to [vercel.com](https://vercel.com)
2. Click "Add New Project"
3. Import Git repository
4. Configure (usually auto-detected)
5. Deploy

</div>

<div class="p-4 bg-green-500 bg-opacity-10 rounded">

**Auto-detected:**
- Build command
- Output directory
- Framework settings
- Environment variables

</div>

</div>

---

# Deployment Options

## 2. Via Vercel CLI

```bash
# Install CLI
npm i -g vercel

# Login
vercel login

# Deploy (from project directory)
vercel                    # Preview deployment
vercel --prod             # Production deployment
```

<div class="mt-4 p-3 bg-blue-500 bg-opacity-10 rounded">

**Tip:** Great for testing deployments before pushing to Git

</div>

---

# Deployment Options

## 3. Via GitHub Actions

```yaml
name: Deploy to Vercel

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Deploy to Vercel
        uses: amondnet/vercel-action@v25
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
          vercel-args: '--prod'
```

---

# vercel.json Configuration

```json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite",
  "rewrites": [
    { "source": "/api/:path*", "destination": "/api/:path*" },
    { "source": "/(.*)", "destination": "/index.html" }
  ],
  "headers": [
    {
      "source": "/api/(.*)",
      "headers": [
        { "key": "Cache-Control", "value": "no-cache" }
      ]
    }
  ],
  "env": {
    "API_URL": "https://api.example.com"
  }
}
```

---

# Environment Variables in Vercel

<div class="grid grid-cols-2 gap-6">

<div>

### Via Dashboard

1. Project Settings -> Environment Variables
2. Add key-value pairs
3. Choose environments:
   - Production
   - Preview
   - Development

</div>

<div>

### Via CLI

```bash
# Add secret
vercel env add API_KEY production

# Pull to local .env
vercel env pull .env.local
```

### In vercel.json

```json
{
  "env": {
    "PUBLIC_API_URL": "https://..."
  }
}
```

</div>

</div>

---

# Preview Deployments

<div class="grid grid-cols-2 gap-8">

<div>

## How It Works

- **Every PR gets a unique URL**
- Perfect for code review
- Automatic comments on PR with preview link
- Environment variables can differ from production

</div>

<div>

## Example URLs

```
main branch
  -> your-app.vercel.app (production)

feature/login PR
  -> your-app-abc123.vercel.app (preview)
```

</div>

</div>

<div class="mt-4 p-3 bg-green-500 bg-opacity-10 rounded">

**Tip:** Use preview deployments to test changes before merging

</div>

---

# Serverless Functions

```
project/
+-- api/
|   +-- hello.js        -> /api/hello
|   +-- users/
|       +-- [id].js     -> /api/users/:id
+-- src/
    +-- ...
```

```javascript
// api/hello.js
export default function handler(req, res) {
  res.status(200).json({ message: 'Hello from Vercel!' });
}
```

<div class="mt-4 p-3 bg-blue-500 bg-opacity-10 rounded">

**Use cases:** Simple APIs, webhooks, form handlers, authentication callbacks

</div>

