FROM python:3.12-slim

# hatch-vcs/setuptools-scm derives the version from git tags. Since the build
# context has no .git, provide a fallback version so the build does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1
ENV LOG_LEVEL=DEBUG

# System dependencies:
# - build-essential: native extension builds (pyaudio, etc.)
# - portaudio19-dev / libasound2-dev: required by pyaudio (bidi feature, part of "all")
# - git: hatch/hatch-vcs tooling and general dev usage
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL dependencies
# (every model provider via "all") plus dev/test tooling (dev).
# Copy the source needed for an editable install.
COPY src ./src

RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
