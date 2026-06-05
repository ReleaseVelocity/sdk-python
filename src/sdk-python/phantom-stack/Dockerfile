FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Without a git repo we must
# supply a pretend version so the editable install can compute one.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps: git (some tooling expects it) and build basics for any
# packages without wheels (e.g. pyaudio is NOT in default extras here).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata resolution.
COPY src ./src

# Install the project in editable/development mode with all runtime extras
# and the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
