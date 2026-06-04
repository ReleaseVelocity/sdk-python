FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repo in this environment, so provide a pretend version to satisfy the build
# backend during the editable install.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System dependencies:
# - build-essential + portaudio19-dev are required to build pyaudio (bidi extra)
# - git is required by hatch-vcs at build time
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install metadata resolution).
COPY src ./src

# Initialize a throwaway git repo so hatch-vcs can resolve a version, then
# install the project in editable/development mode with ALL optional features
# plus dev and bidirectional-streaming dependencies so tests, linters, and
# type-checkers are all available.
RUN git init -q && git add -A && \
    git -c user.email=dev@local -c user.name=dev commit -qm "init" && \
    git tag v0.0.0 && \
    pip install -e ".[all,dev,bidi-all]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
