FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no .git,
# so we provide a pretend version (overridable at build time).
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

# pyaudio (from bidi extra) needs portaudio headers + a C toolchain.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
