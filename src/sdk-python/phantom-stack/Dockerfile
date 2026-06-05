FROM python:3.12-slim

# Avoid interactive prompts and enable faster, cleaner pip behavior
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The build context may not be a
# git repository, so provide a fallback version that hatchling can use.
ARG STRANDS_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${STRANDS_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}

# System deps: git (for hatch-vcs when available) and portaudio for the optional
# bidi (pyaudio) feature compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install with hatchling).
COPY src ./src

# Install the project in editable/development mode with ALL extras plus the
# dev tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
