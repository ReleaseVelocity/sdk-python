FROM python:3.12-slim

# System dependencies:
# - git: hatch-vcs derives the package version from git tags
# - build tools + portaudio/alsa headers: required to build pyaudio (bidi feature)
#   and any packages without prebuilt wheels
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    build-essential \
    portaudio19-dev \
    libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling first for best wheel resolution
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first to leverage layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / version resolution)
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs a git repo with a tag to compute a version.
# This repo is exported without .git, so initialize a throwaway one
# and pin the version explicitly to keep the build hermetic.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional features
# (all = a2a, anthropic, docs, gemini, litellm, llamaapi, mistral, ollama,
#  openai, writer, sagemaker, otel) plus the dev/test toolchain, and the bidi
# extras so audio-based experimental modules are importable.
RUN pip install --no-cache-dir -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

# Sensible defaults so tests relying on AWS clients don't hang on credentials
ENV AWS_DEFAULT_REGION=us-west-2
ENV LOG_LEVEL=INFO

CMD ["sleep", "infinity"]
