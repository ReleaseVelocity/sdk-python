FROM python:3.12-slim

# git is required because the project uses hatch-vcs (version from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / packages config)
COPY src ./src

# The project uses hatch-vcs to determine its version from git tags.
# Initialize a minimal git repo with a tag so the build can resolve a version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "init" && \
    git tag v1.0.0

# Upgrade pip and install the project in editable/dev mode with ALL extras + dev tools
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

# Re-commit any newly added files so git state stays clean for hatch-vcs
RUN git add -A && git commit -q -m "full source" || true

CMD ["sleep", "infinity"]
