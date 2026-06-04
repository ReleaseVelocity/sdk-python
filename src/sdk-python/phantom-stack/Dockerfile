FROM python:3.12-slim

# System deps: git needed for hatch-vcs versioning; build tools for any native wheels
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source (needed for editable install)
COPY src/ ./src/

# hatch-vcs needs a git repo with a tag to determine version.
# Initialize a repo and create a tag so the build doesn't fail on dynamic version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL extras + dev/test tooling.
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs)
COPY . .

# Re-commit any newly added files so the working tree is clean for hatch-vcs
RUN git add -A && git commit -qm "full source" || true

CMD ["sleep", "infinity"]
