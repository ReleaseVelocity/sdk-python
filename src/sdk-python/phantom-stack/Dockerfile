FROM python:3.12-slim

# Version is normally derived from git tags via hatch-vcs. There is no .git in
# this checkout, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs/hatchling can introspect the repo (harmless if absent)
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve packages).
COPY src/ ./src/

# Install the project in editable/development mode with ALL runtime extras,
# the bidi extras, and the dev/test tooling.
RUN pip install -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
