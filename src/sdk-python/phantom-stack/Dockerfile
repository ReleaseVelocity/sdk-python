FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here has no
# .git directory, so we provide a pretend version to keep the build working.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System dependencies:
#  - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi feature)
#  - gcc/build-essential: native builds for some transitive deps
#  - git: hatch-vcs / general tooling
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        gcc \
        git \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads packages = ["src/strands"]).
COPY src ./src

# Install the project in editable/development mode with ALL dependency groups:
#   all      -> every model/provider optional dependency
#   bidi-all -> bidirectional streaming deps (needs Python >= 3.12)
#   dev      -> pytest, ruff, mypy, moto, hatch, pre-commit, etc.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
