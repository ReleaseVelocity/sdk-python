FROM python:3.12-slim

# System deps:
# - git: hatch-vcs may inspect repo; also useful for dev
# - build-essential: in case any dependency needs native compilation
# - portaudio19-dev / libasound2-dev: required by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so we provide an explicit version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_NO_CACHE_DIR=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install of the project).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev tools.
# [all] pulls in every model-provider extra; [bidi] adds the audio stack;
# [dev] adds pytest, ruff, mypy, moto, hatch, etc.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
