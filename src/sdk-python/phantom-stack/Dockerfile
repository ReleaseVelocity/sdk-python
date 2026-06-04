FROM python:3.12-slim

# Avoid interactive prompts and ensure unbuffered Python output
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps:
# - git: required by hatch-vcs to determine the package version from tags
# - build-essential: in case any dependency needs to compile native extensions
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags. The source tree is not a git repo,
# so we initialize one with a tag before installing in editable mode.
# We also pre-populate the version so an editable install works without re-running git.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v1.0.0

# Install the project in editable/development mode with ALL optional feature
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
