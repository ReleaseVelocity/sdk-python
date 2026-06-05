FROM python:3.12-slim

# System deps:
# - git: required because hatch-vcs reads version from git (we also set a pretend version as fallback)
# - portaudio19-dev + gcc: needed to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Fallback version for hatch-vcs/setuptools-scm when no git tags/.git are present.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / hatchling build).
COPY src ./src

# Install the project in editable/dev mode with ALL optional features and dev tools.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
