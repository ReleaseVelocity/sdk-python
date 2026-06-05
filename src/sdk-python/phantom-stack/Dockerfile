FROM python:3.13-slim

# Avoid interactive prompts and ensure clean output
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version so the build/install does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System deps: build tools for any native wheels (e.g. pyaudio needs portaudio),
# and git for tooling that may expect it.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolution works during
# dependency resolution (full source copied below).
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with ALL extras + dev tools.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the full project source.
COPY . .

# Re-run editable install to pick up full package metadata.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
