FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The source tree shipped
# here has no .git directory, so provide an explicit pretend version.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build tooling: git is referenced by hatch-vcs; build-essential covers any
# native wheels that lack prebuilt binaries.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install can resolve metadata
# before the full source is copied (kept minimal; real source copied below).
COPY src/ ./src/

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
