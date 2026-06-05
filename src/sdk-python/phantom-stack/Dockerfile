FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The source tree is not
# a git checkout in this environment, so provide the version explicitly via the
# setuptools_scm pretend-version env var (hatch-vcs reads setuptools_scm).
# The dist name "strands-agents" normalizes to "STRANDS_AGENTS".
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed so hatch-vcs/setuptools_scm can resolve a version at build time.
RUN apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed in editable
# mode and hatchling reads src/strands during the build).
COPY src ./src

# Initialize a throwaway git repo with a tag so hatch-vcs can derive a version
# deterministically, and install the project in editable/development mode with
# ALL optional feature sets plus the dev/test toolchain.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "build" \
    && git tag v0.0.0 \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
