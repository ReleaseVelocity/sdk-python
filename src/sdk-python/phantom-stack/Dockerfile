FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context is not a git
# repo, so provide an explicit fallback version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is needed by hatch-vcs / tooling; build tools occasionally needed for wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the version).
COPY src ./src

# Initialize a git repo with a tag so hatch-vcs can resolve a version cleanly.
RUN git init -q && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A && git commit -qm "build" \
    && git tag v0.0.0

# Install the project in editable/development mode with ALL dependencies,
# including the full dev/test toolchain and every optional provider extra.
RUN pip install --no-cache-dir -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
