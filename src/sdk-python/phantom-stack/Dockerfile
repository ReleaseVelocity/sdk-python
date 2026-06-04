FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain a usable git history, provide a pretend version so the build succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling. git is needed by hatch-vcs; build-essential covers any
# packages that need to compile native extensions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package layout referenced by the build backend so the editable
# install can resolve metadata before the full source is copied.
COPY src/ ./src/

# Install the package in editable/development mode with ALL runtime extras and
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
