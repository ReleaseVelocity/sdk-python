FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version (overridable via build arg) so the
# editable install can succeed.
ARG SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION}

# Some optional deps (e.g. pyaudio under bidi) need build/audio headers; the
# core "all" + "dev" set installs cleanly with just build-essential available.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in the full source so the editable install resolves packages.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install in editable/development mode with all runtime extras and dev tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
