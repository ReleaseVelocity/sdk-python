FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source copied into the image
# has no git history, so provide a fallback version for hatch-vcs/setuptools_scm.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# System build deps. portaudio/alsa are only needed for the optional "bidi"
# extras (pyaudio); kept minimal since the default test suite excludes bidi.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads the package at install time).
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ
COPY docs ./docs

# Install the project in editable/development mode with all optional model
# providers plus the dev/test toolchain (pytest, ruff, mypy, moto, hatch, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
