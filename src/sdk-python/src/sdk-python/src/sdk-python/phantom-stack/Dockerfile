FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context may not include
# git metadata, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# System deps:
# - git: hatch-vcs versioning support
# - build-essential: native builds for some optional deps
# - portaudio19-dev: required to build pyaudio (bidi extras)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves during dep step.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all runtime + optional + dev dependencies in editable/dev mode.
# "all" pulls every model provider extra; "dev" pulls test & lint tooling.
RUN pip install -e ".[all,dev]"

# Copy the full project source.
COPY . .

# Re-run editable install so the real package metadata/entrypoints are wired up.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
