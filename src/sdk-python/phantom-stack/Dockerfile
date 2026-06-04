FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# .git history, provide a pretend version so the build backend succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# pyaudio (pulled in by the bidi extra, part of all-deps resolution) needs
# portaudio headers; git is kept available for tooling/pre-commit.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Editable install with ALL optional features plus dev/test tooling.
# We copy the package source needed for the editable build to resolve.
COPY src/ ./src/
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
