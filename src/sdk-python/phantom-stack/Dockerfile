FROM python:3.11-slim

# hatch-vcs (setuptools-scm) derives the version from git tags. The build
# context has no .git history, so provide a pretend version so the editable
# install of strands-agents succeeds. Configurable via build arg.
ARG STRANDS_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}

# System deps: git is referenced by hatch-vcs; portaudio is needed by pyaudio
# (pulled in by the bidi extras). Keep it minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because pip resolves the editable install
# against the real package layout).
COPY src ./src

# Install the project in editable mode with all runtime extras plus the dev/
# test toolchain (pytest, moto, mypy, ruff, hatch, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
