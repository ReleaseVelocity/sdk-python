FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Allow overriding so the build
# does not depend on the project carrying its git history.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs; build tooling kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata) and tests.
COPY src ./src

# Initialise a git repo with a tag so hatch-vcs can resolve a version even if
# HATCH_VCS_PRETEND_VERSION is unset in derived environments.
RUN git init -q && git config user.email dev@example.com && git config user.name dev \
    && git add -A && git commit -qm "init" && git tag v0.0.0

# Install the project in editable/development mode with all optional and dev
# dependencies so tests, linters, and type checkers are all available.
# 'bidi' is intentionally excluded because pyaudio requires system PortAudio
# and is not part of the default test/lint environment.
RUN pip install -e ".[all,dev]"

# Bring in the remaining project files (tests, configs, docs, etc.).
COPY . .

CMD ["sleep", "infinity"]
