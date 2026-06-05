FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git metadata. The source
# tree may not ship a .git directory, so we also provide a pretend version as a
# fallback. This is wired through a build arg so it stays configurable.
ARG PKG_PRETEND_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps:
# - git: required by hatch-vcs to compute the package version
# - gcc + portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies for caching. The actual editable install below picks
# up the full source tree. We initialize a throwaway git repo so hatch-vcs can
# resolve a version during the editable install when no .git is present.
COPY . /app

# Ensure hatch-vcs has a git history with a tag to derive the version from.
RUN if [ ! -d /app/.git ]; then \
        git init -q /app && \
        git -C /app config user.email "dev@example.com" && \
        git -C /app config user.name "dev" && \
        git -C /app add -A && \
        git -C /app commit -q -m "containerize snapshot" && \
        git -C /app tag "v${PKG_PRETEND_VERSION}" ; \
    fi

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling so a developer can run tests, linters, type checks.
# This mirrors the project's hatch-test env (features=["all"]) plus the dev group.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
