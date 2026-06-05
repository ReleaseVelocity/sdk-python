FROM python:3.12-slim

# pyaudio (bidi extra) needs portaudio headers; build tools for any native deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# hatch-vcs derives the version from git tags. There is no git history here,
# so provide a pretend version to allow the editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with all optional features
# and the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
