FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source tree may not
# include .git, provide a pretend version so the build/install succeeds.
ARG PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${PRETEND_VERSION}

# pyaudio (bidi extra) needs portaudio headers; build tools for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        build-essential \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for editable install metadata.
COPY src ./src

# Install the project in editable/dev mode with all + dev dependencies.
# "all" pulls every model-provider extra; "dev" adds pytest, ruff, mypy, moto, etc.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Keep the container alive for interactive development.
CMD ["sleep", "infinity"]
