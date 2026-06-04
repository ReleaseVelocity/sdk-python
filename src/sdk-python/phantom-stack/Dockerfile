FROM python:3.13-slim

# hatch-vcs uses setuptools_scm under the hood to derive the version from git
# tags. Since the build context may not carry git history, provide a pretend
# version so the editable install succeeds deterministically.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal system deps. pyaudio (bidi extra) needs portaudio + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install with hatchling).
COPY src ./src

# Install the project in editable/development mode with all runtime extras,
# dev/test tooling. This pulls pytest, ruff, mypy, moto, etc.
RUN pip install -e ".[dev,all]"

# Copy remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
