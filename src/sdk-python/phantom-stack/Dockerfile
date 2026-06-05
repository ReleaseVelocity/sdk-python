FROM python:3.12-slim

# Avoid interactive prompts and keep Python output unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the package version from git tags. The source tree is
# shipped without a .git directory, so provide a pretend version to satisfy
# the build backend (hatchling + hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System build deps: pyaudio (bidi extra) needs portaudio headers + a compiler.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to maximize layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project is installed editable
# and hatchling reads the package layout at install time).
COPY src ./src

# Install the project in editable/dev mode with ALL optional features plus
# the dev tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
