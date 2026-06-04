FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree provided to the
# container is not a git checkout, so we tell hatch-vcs/setuptools_scm a
# fallback version to use instead. Kept as an ARG/ENV so it can be overridden.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

# Avoid interactive prompts and keep Python output unbuffered for dev ergonomics
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed by hatch-vcs at build/install time; build-essential covers any
# packages that need to compile native extensions.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package path so the editable install metadata resolves while
# we install dependencies (full source is copied right after).
COPY src ./src

# Install the project in editable/development mode with ALL optional model
# providers ("all") plus the dev/test tooling ("dev"). The "all" extra
# deliberately excludes the experimental "bidi" feature, which requires
# Python-version-gated native audio libraries (pyaudio/portaudio).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Default to an interactive shell so developers can exec in and run tests,
# linters, or any project command.
CMD ["bash"]
