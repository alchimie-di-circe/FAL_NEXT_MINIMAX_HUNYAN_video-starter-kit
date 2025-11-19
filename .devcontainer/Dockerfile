# Use official Node.js DevContainer image
FROM mcr.microsoft.com/devcontainers/javascript-node:1-20-bookworm

# Install direnv for environment variable management
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends \
    direnv \
    curl \
    git \
    gnupg \
    lsb-release \
    ca-certificates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install DigitalOcean CLI (doctl)
RUN curl -sL https://github.com/digitalocean/doctl/releases/download/v1.104.0/doctl-1.104.0-linux-amd64.tar.gz | tar -xzv \
    && mv doctl /usr/local/bin/doctl \
    && chmod +x /usr/local/bin/doctl

# Switch to node user (already exists in base image)
USER node

# Configure direnv for the node user
RUN echo 'eval "$(direnv hook bash)"' >> /home/node/.bashrc \
    && echo 'eval "$(direnv hook zsh)"' >> /home/node/.zshrc 2>/dev/null || true

# Create directories for persistent data
RUN mkdir -p /home/node/.bash_history_data \
    && mkdir -p /home/node/.config/direnv \
    && mkdir -p /home/node/.claude

# Configure bash history persistence
RUN echo 'export HISTFILE=/home/node/.bash_history_data/.bash_history' >> /home/node/.bashrc \
    && echo 'export PROMPT_COMMAND="history -a; $PROMPT_COMMAND"' >> /home/node/.bashrc

# Set working directory
WORKDIR /workspace

# Install global npm packages for development
RUN npm install -g \
    typescript \
    ts-node \
    @biomejs/biome \
    vercel

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD node --version || exit 1

# Default command (overridden by docker-compose)
CMD ["sleep", "infinity"]
