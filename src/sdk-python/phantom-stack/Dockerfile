FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repo in this environment, so provide a fallback version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System deps:
# - git: hatch-vcs / general tooling
# - portaudio19-dev, libasound2-dev: audio libs used by the optional bidi (pyaudio) extras
# - build-essential: in case any dependency needs to compile from source
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install).
COPY src ./src

# Install the project in editable/development mode with all model providers
# plus the dev/test toolchain (pytest, ruff, mypy, moto, pytest-asyncio, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
