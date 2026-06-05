FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Source is copied without .git,
# so provide a pretend version for setuptools-scm (used by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# System dependencies:
# - git: required by hatch-vcs tooling
# - build-essential, portaudio19-dev, libasound2-dev: needed to build pyaudio
#   (part of the "bidi" extra pulled in by the "all" feature)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling references src/strands at build time).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus dev/test tooling so a developer can run tests, linters, and any command.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
