FROM python:3.12-slim

# System dependencies:
# - git: hatch-vcs reads version from git (we also pretend a version below as fallback)
# - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi extra)
# - gcc/build tools: for any source builds of wheels
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version that hatchling/hatch-vcs will honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata generation).
COPY src ./src

# Install the project in editable/development mode with ALL optional features,
# the bidi stack, and dev/test tooling so tests and linters are available.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
