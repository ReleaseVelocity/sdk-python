FROM python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. The build context
# has no git metadata, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    LOG_LEVEL=DEBUG

# System dependencies:
# - portaudio19-dev / libasound2-dev: required by pyaudio (bidi feature, part of bidi-all)
# - git: hatch-vcs / general tooling
# - build-essential: native wheel fallbacks (pyaudio etc.)
RUN apt-get update && apt-get install -y --no-install-recommends \
        portaudio19-dev \
        libasound2-dev \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Install hatch (the project's dev workflow tool) up front.
RUN pip install --no-cache-dir hatch

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install to resolve packages).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras plus
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.). bidi-all pulls in the
# bidirectional/audio + a2a + otel features.
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
