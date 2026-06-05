FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git here, so provide
# a pretend version so the build/install does not fail.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# System dependencies:
# - build tools + portaudio/alsa headers are required to build the pyaudio
#   wheel pulled in by the optional "bidi" feature (part of the "all" extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching. README is referenced
# by pyproject (readme = "README.md") so it must be present for the install.
COPY pyproject.toml README.md ./

# Pre-create the package source path so the editable install metadata resolves.
COPY src/ ./src/

# Install the project in editable/dev mode with ALL runtime extras plus the
# dev/test toolchain (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the source (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
