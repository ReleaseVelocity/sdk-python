FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context may not include
# a .git directory, so provide a pretend version to satisfy the build backend.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
# setuptools-scm (used under hatch-vcs) honors a dist-name-specific pretend var.
# Dist name "strands-agents" normalizes to "STRANDS_AGENTS".
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# PyAudio (pulled in by the optional "bidi" extra via the "all"/"bidi-all" sets is
# not part of "all") and general native builds may need a compiler + portaudio headers.
# Install minimal build tooling; remove apt lists afterwards to keep the image small.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve the package
# before the full source is copied (hatchling needs src/strands present).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Default to an interactive shell so a developer can exec in and run tooling.
CMD ["sleep", "infinity"]
