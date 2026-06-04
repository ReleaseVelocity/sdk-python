FROM python:3.12-slim

# System deps:
# - git: required because hatch-vcs derives the version from git metadata
# - build-essential: for any packages needing native compilation
# - portaudio19-dev: required by pyaudio (bidi optional extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools_scm-based) needs a version. With no git tags present,
# provide a pretend version so the build/editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install with hatchling)
COPY src ./src

# Initialize a git repo so hatch-vcs can resolve a version even without the
# original history, and install the project in editable/development mode with
# all runtime + optional + dev dependencies.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "snapshot" && \
    git tag v0.0.0 && \
    pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
