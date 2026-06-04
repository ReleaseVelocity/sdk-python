FROM python:3.12-slim

# Avoid interactive prompts and ensure stdout is unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags; no .git is present in the build
# context, so provide a pretend version (read by setuptools_scm under the hood).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System deps: portaudio is required to build/import pyaudio (bidi extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        ffmpeg \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install all runtime + optional + dev dependencies in editable mode.
# A minimal package stub is needed for editable install resolution before
# the full source is copied; copy the src tree, then install.
COPY src ./src
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, etc.).
COPY . .

# Reinstall editable to pick up any metadata after full copy (cheap, cached deps).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
