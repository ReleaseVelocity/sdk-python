FROM python:3.12-slim

# hatch-vcs / setuptools_scm derives the version from git tags. There is no
# git repository inside the build context, so provide an explicit version.
ARG PACKAGE_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Install build tooling required by some optional deps and useful dev utilities.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
