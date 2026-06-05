FROM python:3.11-slim

# Avoid interactive prompts and ensure clean Python output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to allow the editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# System deps:
# - git: hatch-vcs/version tooling expects it to be available
# - portaudio19-dev + gcc: required to build pyaudio (pulled in by bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution)
COPY src ./src

# Install the project in editable/dev mode with ALL optional + dev dependencies.
# "all" pulls every model provider + docs + otel; "dev" pulls test/lint tooling.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
