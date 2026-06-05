FROM python:3.12-slim

# Avoid interactive prompts and ensure deterministic Python behavior
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs determines the version from git tags. There is no git history in this
# build context, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System dependencies:
# - build-essential / portaudio19-dev / libasound2-dev: required to build pyaudio
#   (used by the optional "bidi" audio feature) and other native extensions.
# - git: hatch / hatch-vcs and some tooling expect git to be present.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install of the real package).
COPY src ./src

# Install the project in editable/development mode with ALL features and the
# full dev/test toolchain (pytest, moto, mypy, ruff, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]" \
    && pip install hatch

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
