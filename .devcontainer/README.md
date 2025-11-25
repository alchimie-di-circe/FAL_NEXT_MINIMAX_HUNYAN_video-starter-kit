# AI Video Starter Kit - DevContainer Setup

This directory contains the complete DevContainer configuration for local development with integrated secrets management via **direnv** (local) and **1Password Connect** (production/team).

## 📋 Table of Contents

- [Quick Start](#quick-start)
- [Architecture](#architecture)
- [Secrets Management](#secrets-management)
  - [Option 1: direnv (Local Development)](#option-1-direnv-local-development)
  - [Option 2: 1Password Connect (Production/Team)](#option-2-1password-connect-productionteam)
- [Cloud Services Integration](#cloud-services-integration)
- [Troubleshooting](#troubleshooting)
- [Advanced Configuration](#advanced-configuration)

---

## Quick Start

### Prerequisites

- **Docker Desktop** installed and running
- **VS Code** with [Dev Containers extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
- **(Optional)** [direnv](https://direnv.net/) installed on host for local secrets
- **(Optional)** [1Password CLI](https://developer.1password.com/docs/cli/) for team secrets

### Initial Setup

1. **Clone the repository** (if not already done):
   ```bash
   git clone <repository-url>
   cd FAL_NEXT_MINIMAX_HUNYAN_video-starter-kit
   ```

2. **Configure environment variables** (choose one method):

   **Method A: Using direnv (Recommended for local development)**
   ```bash
   # Copy the example file
   cp .envrc.example .envrc

   # Edit .envrc with your API keys
   nano .envrc

   # Allow direnv to load the file
   direnv allow
   ```

   **Method B: Using .env.local (Traditional method)**
   ```bash
   # Copy the example file
   cp .env.example .env.local

   # Edit with your API keys
   nano .env.local
   ```

3. **Open in VS Code**:
   ```bash
   code .
   ```

4. **Reopen in Container**:
   - Press `F1` or `Cmd/Ctrl+Shift+P`
   - Select: **"Dev Containers: Reopen in Container"**
   - Wait for the container to build (first time takes ~5-10 minutes)

5. **Start development**:
   ```bash
   npm run dev
   ```

   Access the app at: http://localhost:3000

---

## Architecture

### Container Stack

```
┌─────────────────────────────────────────────────────────┐
│                    VS Code (Host)                       │
└────────────────────┬────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────┐
│          DevContainer (Node.js 20 + Tools)              │
│  ┌─────────────────────────────────────────────────┐   │
│  │  • Next.js App (Port 3000)                      │   │
│  │  • direnv (Environment Manager)                 │   │
│  │  • doctl (DigitalOcean CLI)                     │   │
│  │  • Vercel CLI                                   │   │
│  │  • TypeScript, Biome, ESLint                    │   │
│  └─────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────┘
                     │
                     ▼
           ┌──────────────────┐
           │  External APIs   │
           │  • fal.ai        │
           │  • UploadThing   │
           │  • Vercel KV     │
           └──────────────────┘
```

### File Structure

```
.devcontainer/
├── devcontainer.json       # VS Code DevContainer configuration
├── docker-compose.yml      # Container orchestration
├── Dockerfile              # Custom container image
├── .env.devcontainer       # Non-sensitive defaults
└── README.md               # This file

Root directory:
├── .envrc.example          # direnv template (for local secrets)
├── .env.example            # Traditional .env template
└── .gitignore              # Excludes .envrc and .env.local
```

---

## Secrets Management

### Option 1: direnv (Local Development)

**direnv** automatically loads environment variables when you enter the project directory.

#### Setup direnv on Host

1. **Install direnv**:
   ```bash
   # macOS
   brew install direnv

   # Ubuntu/Debian
   sudo apt install direnv

   # Add to your shell (~/.bashrc or ~/.zshrc)
   eval "$(direnv hook bash)"  # or zsh
   ```

2. **Configure project**:
   ```bash
   # Copy the template
   cp .envrc.example .envrc

   # Edit with your real API keys
   nano .envrc
   ```

3. **Activate**:
   ```bash
   # Allow direnv to load the file
   direnv allow
   ```

#### Required Variables

Edit `.envrc` and set:

```bash
# Required
export FAL_KEY="fal_..."
export UPLOADTHING_TOKEN="sk_..."

# Optional (for share feature)
export KV_URL="redis://..."
export KV_REST_API_TOKEN="..."

# Optional (DigitalOcean)
export DIGITALOCEAN_API_TOKEN="dop_v1_..."
```

#### Verify Setup

```bash
# Check variables are loaded
echo $FAL_KEY

# Inside DevContainer, direnv will auto-load
direnv allow
```

#### Security

- `.envrc` is git-ignored automatically
- Never commit `.envrc` to version control
- Use `.envrc.example` as a template for team members

---

### Option 2: 1Password Connect (Production/Team)

**1Password Connect** enables secure team-wide secrets management and CI/CD integration.

#### Architecture

```
┌──────────────────┐      ┌──────────────────┐      ┌──────────────────┐
│  1Password Vault │ ───> │ 1Password Connect│ ───> │   DevContainer   │
│  (Secure Storage)│      │   (API Server)   │      │  (Your App)      │
└──────────────────┘      └──────────────────┘      └──────────────────┘
```

#### Setup 1Password Connect

1. **Install 1Password CLI** on host:
   ```bash
   # macOS
   brew install --cask 1password-cli

   # Sign in
   op signin
   ```

2. **Create secrets in 1Password**:
   - Open 1Password desktop app
   - Create a vault named "Development" (or your preference)
   - Add items:
     - **FAL API Key**
       - Name: `FAL_API`
       - Field: `credential` = your fal.ai key
     - **UploadThing Token**
       - Name: `UploadThing`
       - Field: `credential` = your token
     - **DigitalOcean Token**
       - Name: `DigitalOcean`
       - Field: `token` = your DO token

3. **Configure .envrc with 1Password**:
   ```bash
   # Edit .envrc
   nano .envrc

   # Replace static values with 1Password references:
   export FAL_KEY=$(op read "op://Development/FAL_API/credential")
   export UPLOADTHING_TOKEN=$(op read "op://Development/UploadThing/credential")
   export DIGITALOCEAN_API_TOKEN=$(op read "op://Development/DigitalOcean/token")
   ```

4. **Allow direnv**:
   ```bash
   direnv allow
   ```

#### 1Password Connect Server (Production)

For CI/CD and team environments, deploy 1Password Connect Server:

1. **Deploy Connect Server**:
   ```bash
   # Using Docker
   docker run -d \
     --name onepassword-connect \
     -p 8080:8080 \
     -v ~/.1password:/home/opuser/.op \
     1password/connect-api:latest
   ```

2. **Generate Connect Token**:
   ```bash
   # From 1Password dashboard
   # Settings → Integrations → 1Password Connect → Create Token
   ```

3. **Configure in CI/CD** (e.g., GitHub Actions):
   ```yaml
   # .github/workflows/deploy.yml
   jobs:
     deploy:
       steps:
         - name: Load secrets from 1Password
           uses: 1password/load-secrets-action@v1
           with:
             export-env: true
           env:
             OP_CONNECT_HOST: ${{ secrets.OP_CONNECT_HOST }}
             OP_CONNECT_TOKEN: ${{ secrets.OP_CONNECT_TOKEN }}
             FAL_KEY: op://Development/FAL_API/credential
             UPLOADTHING_TOKEN: op://Development/UploadThing/credential
   ```

#### Benefits of 1Password Connect

- ✅ **Team-wide secrets** - Everyone accesses the same vault
- ✅ **Rotation** - Update secrets once, applies everywhere
- ✅ **Audit logs** - Track who accessed what
- ✅ **CI/CD integration** - Seamless GitHub Actions support
- ✅ **No secrets in git** - Ever

#### Comparison: direnv vs 1Password

| Feature | direnv | 1Password Connect |
|---------|--------|-------------------|
| **Use Case** | Solo developer, local only | Team, production, CI/CD |
| **Setup** | Simple, file-based | Requires 1Password subscription |
| **Secrets Rotation** | Manual (edit .envrc) | Centralized (update vault) |
| **Team Sharing** | Share .envrc.example only | Shared vault access |
| **CI/CD** | Not suitable | Native integration |
| **Cost** | Free | $19.95/user/year (Teams plan) |

**Recommendation**: Start with **direnv** for local development, migrate to **1Password Connect** when:
- Working in a team
- Need CI/CD integration
- Require audit logs and compliance

---

## Cloud Services Integration

### DigitalOcean CLI (doctl)

The DevContainer includes **doctl** pre-installed.

#### Setup

1. **Set API token** (in `.envrc` or environment):
   ```bash
   export DIGITALOCEAN_API_TOKEN="dop_v1_..."
   ```

2. **Initialize doctl**:
   ```bash
   # Inside DevContainer
   doctl auth init --access-token $DIGITALOCEAN_API_TOKEN
   ```

3. **Verify**:
   ```bash
   doctl account get
   doctl kubernetes cluster list
   doctl apps list
   ```

#### Common Commands

```bash
# List droplets
doctl compute droplet list

# Deploy to App Platform
doctl apps create --spec .do/app.yaml

# Manage databases
doctl databases list

# Container registry
doctl registry login
```

### Claude Code Integration

Claude Code is **pre-installed** in the DevContainer and automatically configured.

#### Setup

The DevContainer:
- ✅ **Installs Claude Code** during container build
- ✅ **Mounts `~/.claude`** from host for persistent authentication
- ✅ **Preserves configuration** across container rebuilds

#### First-Time Authentication

If Claude Code is not authenticated, run:

```bash
# Inside DevContainer
claude login
```

This will authenticate Claude Code and save credentials to `~/.claude` (which is mounted from your host).

#### Usage

```bash
# Check version
claude --version

# Use Claude Code commands
claude generate "Create a new component"
claude review "Review this code"
claude chat "How do I implement X?"

# Get help
claude --help
```

#### Manual Installation (If Automatic Install Fails)

If the automatic installation during container build fails, you can install manually:

```bash
# Option 1: Using npm (if available)
npm install -g @anthropic-ai/claude-code

# Option 2: Using installation script
curl -fsSL https://repo.claude.ai/install.sh | sh

# Option 3: Download binary directly
curl -L https://github.com/anthropics/claude-code/releases/latest/download/claude-code-linux-x64.tar.gz -o /tmp/claude-code.tar.gz
sudo tar -xzf /tmp/claude-code.tar.gz -C /usr/local/bin
sudo chmod +x /usr/local/bin/claude
rm /tmp/claude-code.tar.gz
```

#### Troubleshooting

**Problem**: `claude: command not found`

**Solutions**:
```bash
# 1. Check if Claude Code is installed
which claude

# 2. Verify PATH includes /usr/local/bin
echo $PATH

# 3. Manually add to PATH if needed
export PATH="/usr/local/bin:$PATH"
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bashrc

# 4. Rebuild container without cache
# VS Code: Cmd+Shift+P → "Dev Containers: Rebuild Container Without Cache"
```

**Problem**: Authentication issues

**Solutions**:
```bash
# 1. Re-authenticate
claude logout
claude login

# 2. Check mounted config
ls -la ~/.claude

# 3. Verify mount in devcontainer.json
# Should have: "source=${localEnv:HOME}/.claude,target=/home/node/.claude,type=bind"
```

Configuration is persistent across container rebuilds via the mounted `~/.claude` directory.

### Vercel CLI

Pre-installed for easy deployments.

```bash
# Login (first time only)
vercel login

# Deploy
vercel

# Production deploy
vercel --prod

# Environment variables
vercel env add FAL_KEY
```

---

## Troubleshooting

### Port 3000 Already in Use

**Problem**: `Error: listen EADDRINUSE: address already in use :::3000`

**Solution**:
```bash
# Find process using port 3000
lsof -i :3000

# Kill the process
kill -9 <PID>

# Or use alternative port
npm run dev -- -p 3001
```

### direnv Not Loading Variables

**Problem**: Variables not available in DevContainer

**Solution**:
```bash
# On host machine
direnv allow

# Rebuild DevContainer
# VS Code: Cmd+Shift+P → "Dev Containers: Rebuild Container"

# Verify inside container
echo $FAL_KEY
```

### 1Password CLI Errors

**Problem**: `op: command not found` or authentication errors

**Solution**:
```bash
# Ensure 1Password CLI is installed on HOST (not container)
brew install --cask 1password-cli

# Sign in
op signin

# Test reading a secret
op read "op://Development/FAL_API/credential"
```

### Container Build Fails

**Problem**: Docker build errors

**Solution**:
```bash
# Clean Docker cache
docker system prune -a

# Rebuild without cache
# VS Code: Cmd+Shift+P → "Dev Containers: Rebuild Container Without Cache"
```

### Missing API Keys

**Problem**: App fails with "Invalid API key"

**Solution**:
```bash
# Verify variables are set
env | grep -E "FAL_KEY|UPLOADTHING"

# Check .envrc syntax
cat .envrc

# Re-allow direnv
direnv allow

# Restart DevContainer
```

---

## Advanced Configuration

### Custom VS Code Extensions

Edit `.devcontainer/devcontainer.json`:

```json
{
  "customizations": {
    "vscode": {
      "extensions": [
        "your-extension-id"
      ]
    }
  }
}
```

### Resource Limits

Adjust in `docker-compose.yml`:

```yaml
services:
  app:
    deploy:
      resources:
        limits:
          cpus: '4'
          memory: 8G
```

### Additional Services (Database Example)

If you need databases in the future, add to `docker-compose.yml`:

```yaml
services:
  postgres:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: devuser
      POSTGRES_PASSWORD: devpass
      POSTGRES_DB: ai_video_dev
    volumes:
      - postgres-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres-data:
```

### Environment-Specific Overrides

Create `.devcontainer/docker-compose.override.yml`:

```yaml
services:
  app:
    environment:
      - DEBUG=true
```

This file is git-ignored and won't affect other developers.

---

## Security Best Practices

### Secrets

- ✅ **Use direnv or 1Password** - Never hardcode secrets
- ✅ **Never commit** `.envrc` or `.env.local`
- ✅ **Rotate regularly** - Change API keys periodically
- ✅ **Use read-only tokens** - Where possible (e.g., KV_REST_API_READ_ONLY_TOKEN)
- ✅ **Limit scope** - Create tokens with minimal permissions

### Container Security

- ✅ **Run as non-root** - Already configured (`user: node`)
- ✅ **Resource limits** - Prevent DoS on host
- ✅ **Keep images updated** - Rebuild containers regularly

### Code Security

- ✅ **Enable ESLint** - Catch security issues early
- ✅ **Run `npm audit`** - Check for vulnerable dependencies
- ✅ **Use Biome** - Enforce code quality

---

## Additional Resources

- [DevContainers Specification](https://containers.dev/)
- [direnv Documentation](https://direnv.net/)
- [1Password Developer](https://developer.1password.com/)
- [DigitalOcean doctl](https://docs.digitalocean.com/reference/doctl/)
- [VS Code Remote Development](https://code.visualstudio.com/docs/remote/remote-overview)

---

## Support

For issues or questions:
- Check [Troubleshooting](#troubleshooting) section
- Review [AGENTS.md](../AGENTS.md) for development guidelines
- Consult [CLAUDE.md](../CLAUDE.md) for project documentation
- Open an issue on GitHub

---

**Last Updated**: 2025-11-19
**Version**: 1.0.0
