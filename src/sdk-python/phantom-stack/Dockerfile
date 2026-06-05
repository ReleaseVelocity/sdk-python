FROM python:3.12-slim

# hatch-vcs derives the version from git tags; the source tree here has no
# git history, so provide a pretend version to allow the build to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# pyaudio (from the bidi extra) needs PortAudio headers; git is useful for dev.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling.
RUN pip install --upgrade pip hatch hatchling hatch-vcs uv

# Copy the full source so the editable install has the package available.
COPY . /app

# Install the project in editable/development mode with all optional and dev
# dependencies so tests, linters, and dev tools are all available.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
