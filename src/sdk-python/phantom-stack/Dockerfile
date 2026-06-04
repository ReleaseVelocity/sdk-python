FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require a real git history/tag.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# System dependencies:
# - git: hatch-vcs / general dev workflows
# - build-essential: native builds for some optional deps
# - portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with ALL optional features,
# the bidi feature set, and dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
