FROM python:3.13-slim

# hatch-vcs derives the version from git tags. This repo ships without a .git
# directory, so provide a pretend version to keep the build deterministic.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs portaudio headers; build-essential covers any
# native wheels that lack prebuilt binaries.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves before
# the full source copy.
COPY src/ ./src/

# Install the project in editable/development mode with every optional group
# plus the dev/test tooling so a developer can run tests, linters, and typing.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
