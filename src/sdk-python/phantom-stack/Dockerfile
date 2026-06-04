FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# System dependencies:
# - git: required by hatch-vcs to determine the package version
# - build-essential: native compilation for some deps
# - portaudio19-dev: required to build pyaudio (used by the bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so initialize a throwaway repo with a tag to give the build a valid version.
# This is also overridable via the HATCH_VCS_PRETEND_VERSION build arg.
ARG HATCH_VCS_PRETEND_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}

# Copy the full source
COPY . /app

# Ensure a git repo + tag exists so hatch-vcs always has a version to resolve
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "container build" && \
    git tag "v${HATCH_VCS_PRETEND_VERSION}" || true

# Install the project in editable/development mode with ALL optional features,
# the full dev toolchain, and the bidi (bidirectional streaming) extras.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

CMD ["sleep", "infinity"]
