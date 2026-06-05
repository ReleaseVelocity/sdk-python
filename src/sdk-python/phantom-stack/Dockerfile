FROM python:3.12-slim

# Install git (needed by hatch-vcs to determine version from tags) and build basics
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest and metadata first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed for editable install and git history)
COPY . .

# hatch-vcs derives version from git tags. The build context is a checkout;
# ensure there's a git repo with at least one tag so install succeeds even if
# the original .git isn't present in the context.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL extras + dev deps
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
