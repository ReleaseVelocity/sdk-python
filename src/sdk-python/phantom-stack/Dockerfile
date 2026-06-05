FROM python:3.12-slim

# hatch-vcs/setuptools_scm derives the version from git tags. The source tree
# has no .git directory, so provide a pretend version for the build.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System deps:
# - portaudio19-dev: required to build pyaudio (bidi extra)
# - gcc/build-essential: for any native wheels lacking prebuilt binaries
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve packages).
COPY src ./src

# Install the project in editable/development mode with ALL dependencies,
# including the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
