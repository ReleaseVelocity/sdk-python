FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a fallback so the
# editable install succeeds even without git history present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt but some transitive deps
# may need a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/dev mode with all optional features plus the
# dev (test/lint) toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
