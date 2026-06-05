FROM python:3.12-slim

# System deps:
# - git: hatch-vcs derives the package version from git tags at build time
# - build-essential: some transitive deps (e.g. pyaudio is excluded, but others may compile)
# - portaudio19-dev: required headers if pyaudio-related extras are pulled
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source.
COPY . /app

# hatch-vcs requires a git repository with a tag to compute the version.
# The original project's .git is not present in this workspace, so we
# bootstrap a minimal repo with an initial version tag.
RUN if [ ! -d /app/.git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -q -m "containerize" && \
        git tag v1.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip setuptools wheel && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
