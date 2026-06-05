FROM python:3.13-slim

# hatch-vcs determines the package version from git tags. The source tree is
# provided without git history, so supply a pretend version so the editable
# install succeeds. Can be overridden at build time via --build-arg.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# Minimal build tooling. Most deps ship wheels; git is required by hatch-vcs.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install build backend).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.).
COPY . .

CMD ["bash"]
