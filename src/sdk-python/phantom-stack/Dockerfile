FROM python:3.13-slim

# hatch-vcs derives the package version from git tags, so git + a tag are
# required for the editable install to succeed.
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    LOG_LEVEL=DEBUG \
    # Allow hatch-vcs to resolve a version even on a fresh git repo without tags
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# System build deps:
#  - git: required by hatch-vcs for version resolution
#  - build-essential: native builds for some transitive deps
#  - portaudio19-dev / libasound2-dev: required by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install hatch (CI uses it) and warm the dependency layer.
RUN pip install hatch

# Copy the full source tree.
COPY . /app

# Initialise a git repo with a tag so hatch-vcs can resolve a version,
# then install the project in editable/development mode with ALL optional
# dependencies plus dev/test tooling.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "containerize" && \
    git tag v0.0.0 && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
