FROM python:3.12-slim

# Avoid interactive prompts and ensure clean pip behaviour
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps:
# - git: required by hatch-vcs to derive the package version from tags
# - build-essential: in case any sdist-only dependency needs compilation
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip tooling
RUN python -m pip install --upgrade pip setuptools wheel

# ---------------------------------------------------------------------------
# Dependency layer: copy only the manifest first to leverage build caching.
# hatch-vcs needs a git repo with a tag to compute a version, so we initialise
# a throwaway repo and tag it before installing in editable mode.
# ---------------------------------------------------------------------------
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src/ ./src/

# Initialise a git repo + tag so hatch-vcs can resolve a version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN python -m pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs, etc.)
COPY . .

# Ensure the previously created git tag/state still resolves a version after
# the full source copy (the .git dir created above is preserved).
RUN git add -A && git commit -qm "full source" --allow-empty

CMD ["sleep", "infinity"]
