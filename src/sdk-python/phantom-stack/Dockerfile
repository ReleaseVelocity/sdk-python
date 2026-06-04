FROM python:3.12-slim

# System dependencies:
# - git: required by hatch-vcs to determine version from git history
# - build-essential: for compiling native extensions (e.g. pyaudio)
# - portaudio19-dev: required by pyaudio (bidi feature)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. This repo has no tags, so we
# provide a fallback version via the standard setuptools-scm pretend variable
# that hatch-vcs honors. Configurable via build arg / runtime env.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
