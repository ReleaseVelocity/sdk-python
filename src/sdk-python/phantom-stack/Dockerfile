FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here has no
# .git directory, so provide a pretend version to allow the build to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling. Most deps ship wheels; git is kept available in case
# tooling expects it.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with all optional features
# and the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
