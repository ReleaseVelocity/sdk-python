FROM python:3.12-slim

# Git is needed because the project uses hatch-vcs (version from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source needed for editable install
COPY src ./src

# hatch-vcs derives the version from git tags; with no real repo we
# initialize one and create a tag so the build can resolve a version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL extras + dev/test deps
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
