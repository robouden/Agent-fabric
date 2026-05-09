---
name: docker
description: "Structured workflow for creating and managing Dockerfiles, docker-compose configurations, and container environments."
version: 0.1.0
metadata:
  hermes:
    category: skill
---
# Docker Skill

## Capabilities
- **Dockerfiles** — create optimized, multi-stage build files
- **Docker Compose** — define multi-container applications
- **Image optimization** — minimize image size and layers
- **.dockerignore** — exclude unnecessary files from build context

## Workflow

### Step 1 — Understand the Application
Before writing any Docker configuration:
- Identify the language, framework, and runtime requirements
- Determine build steps (compile, bundle, etc.) vs runtime needs
- List all services the application depends on (database, cache, queue, etc.)
- Check for platform constraints (ARM64/Apple Silicon vs x86_64)

### Step 2 — Create the Dockerfile
Write an optimized Dockerfile following this structure:

1. **Choose the right base image** — pin a specific version, prefer slim/alpine variants
2. **Set up the build stage** — install dependencies, copy source, compile
3. **Set up the runtime stage** — copy only artifacts needed to run
4. **Configure security** — run as non-root user, drop capabilities
5. **Add health check** — `HEALTHCHECK CMD` for production readiness

```dockerfile
# Example: multi-stage build
FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM node:20-alpine AS runtime
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
USER node
HEALTHCHECK CMD wget -qO- http://localhost:3000/health || exit 1
EXPOSE 3000
CMD ["node", "dist/index.js"]
```

### Step 3 — Create .dockerignore
Exclude files that should not enter the build context:
```
.git
node_modules
*.md
.env*
.github
dist
coverage
```

### Step 4 — Create docker-compose.yml
For multi-service applications, define all services:
- Map ports, volumes, and environment variables
- Set dependency order with `depends_on` and health checks
- Use named volumes for persistent data (databases)
- Configure networks for service isolation when needed

### Step 5 — Validate
After writing Docker configuration:
- Build the image: `docker build -t app .`
- Run the container and verify the app starts correctly
- Check image size — look for optimization opportunities
- Test with `docker compose up` if using Compose
- Verify health checks pass

## Layer Optimization Rules
1. Order instructions from least to most frequently changing
2. Copy dependency manifests before source code (cache install step)
3. Combine RUN commands with `&&` to reduce layers
4. Clean up package manager caches in the same RUN step
5. Use `.dockerignore` to shrink build context

## Anti-Patterns to Avoid
- ❌ Using `latest` tag for base images
- ❌ Running as root in production containers
- ❌ Copying the entire source into the runtime stage
- ❌ Storing secrets in the image or Dockerfile
- ❌ Using alpine for languages with native extensions without testing (glibc vs musl)
- ❌ Missing `.dockerignore` (sending .git and node_modules to build context)

## When to Use
- Containerizing an application for deployment
- Setting up local development environments with multiple services
- Creating reproducible build environments
- Configuring CI/CD pipelines that build Docker images

