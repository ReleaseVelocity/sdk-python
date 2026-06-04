FROM python:3.11-slim

# hatch-vcs (via setuptools-scm) derives the version from git tags. The build
# context is not a git repo, so initialize one with a tag below so the version
# resolves naturally during the editable install.
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling. Most deps are wheels; git kept for any vcs needs.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves before full copy.
RUN mkdir -p src/strands

# Copy the full source.
COPY . /app

# Initialize a git repo with a version tag so hatch-vcs/setuptools-scm can
# resolve a version for the editable install.
RUN git init -q && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A && git commit -qm "build" \
    && git tag v0.0.0

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
