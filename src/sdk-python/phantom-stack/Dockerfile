FROM python:3.12-slim

# git is needed because the package version is derived from git tags via hatch-vcs
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the project source
COPY . /app

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so create a minimal repo with a tag to satisfy the build backend.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL extras + dev/test deps
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
