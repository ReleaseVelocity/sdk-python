FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no .git
# directory, so provide a pretend version to allow the editable install to build.
# hatch-vcs uses setuptools-scm under the hood for version detection.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs portaudio headers; git is useful for dev workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable build backend has something to map.
COPY src ./src

# Install the project in editable/development mode with ALL optional extras
# plus the dev toolchain (pytest, moto, ruff, mypy, hatch, etc.).
RUN pip install -e ".[all,dev]"

# Bring in the rest of the source (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
