FROM python:3.12-slim

# Avoid interactive prompts and keep Python output unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs (setuptools_scm) determines the version from git tags. Since the
# source tree may not contain git history, provide a pretend version so the
# editable install succeeds deterministically. Can be overridden at build time.
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

# System deps:
# - git: hatch-vcs fallback / general dev tooling
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution)
COPY src ./src

# Upgrade pip tooling and install the project in editable/development mode with
# the full dependency set: all model providers + dev/test tools + bidi extras.
RUN python -m pip install --upgrade pip setuptools wheel \
    && python -m pip install -e ".[all,dev,bidi-all]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
