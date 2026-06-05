FROM python:3.12-slim

# hatch-vcs (setuptools-scm) determines the version from git tags. Since the
# build context has no .git, provide a pretend version so the build succeeds.
ARG PACKAGE_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION}

# System deps: git for any VCS-based tooling; build tools only if a dependency
# needs to compile. Keep the layer lean.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install resolving metadata).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras and
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.). This mirrors the
# hatch-test environment (features = ["all"] + test deps).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
