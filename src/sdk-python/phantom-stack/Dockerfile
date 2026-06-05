FROM python:3.12-slim

# pyaudio (bidi extra) needs portaudio headers; build tools for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools-scm based) derives the version from git tags. The source
# tree here is not a git repository, so provide an explicit version to the build
# backend via the setuptools-scm pretend-version env vars.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed in editable
# mode and hatchling reads packages = ["src/strands"]).
COPY src ./src

# Install the project in editable/development mode with ALL optional features,
# dev/test tooling, and the bidirectional streaming extras.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
