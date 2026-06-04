FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory in this environment, so provide a pretend version for the build.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
# hatch-vcs is backed by setuptools-scm, which reads this normalized env var.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

# pyaudio (bidi extra) needs portaudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in the source needed for the editable install (hatchling reads it).
COPY src ./src

# hatch-vcs/setuptools-scm needs a git repo with a tag to derive the version.
# This environment ships the source without a .git directory, so create a
# minimal repo with a version tag to satisfy the build backend.
RUN git init -q && \
    git config user.email "build@example.com" && \
    git config user.name "build" && \
    git add -A && \
    git commit -qm "build" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
