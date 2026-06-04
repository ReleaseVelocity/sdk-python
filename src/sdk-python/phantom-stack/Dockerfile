FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version (consumed by setuptools-scm/hatch-vcs) to allow
# the editable install to succeed.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV HATCH_VCS_PRETEND_VERSION=${PKG_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# portaudio is required only if the optional "bidi" extra (pyaudio) is installed.
# We do not install bidi here (its tests are excluded by the project's pytest
# config), so only minimal tooling is required.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Install dependencies first for better layer caching. Copy only the manifest
# and the package metadata needed to resolve dependencies.
COPY pyproject.toml README.md ./

# Editable install with dev tooling + all model-provider extras. The test suite
# imports provider SDKs (anthropic, openai, litellm, ...) directly, matching the
# project's hatch-test env which uses features=["all"].
# Source is copied afterwards; install src package layout placeholder first.
COPY src ./src

RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Copy the remaining project files (tests, configs).
COPY . .

CMD ["sleep", "infinity"]
