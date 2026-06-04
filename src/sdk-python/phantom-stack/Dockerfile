FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. This source tree is not
# a git checkout, so provide a pretend version so the build can succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: build-essential + portaudio for pyaudio (bidi extra),
# git for pre-commit/dev workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest + sources first for better layer caching.
# hatchling references README.md and src/strands during the build.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with all runtime extras,
# the bidi extras, and the dev/test toolchain.
RUN pip install -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
