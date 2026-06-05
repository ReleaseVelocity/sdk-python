FROM python:3.12-slim

# git is needed by hatch-vcs to determine the package version from tags
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so create a throwaway repo with a tag to give it a valid version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "build" && \
    git tag v0.0.0

# Install the package in editable/dev mode with all optional features plus the
# dev (test/lint) tooling so a developer can run tests, linters, etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
