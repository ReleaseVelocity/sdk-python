FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version (configurable per environment).
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${HATCH_VCS_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps:
#  - git: hatch-vcs / pre-commit workflows
#  - portaudio19-dev: required to build pyaudio (bidi extra), harmless otherwise
#  - gcc/build-essential: native builds for some transitive deps
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling + project dependencies. We install the project itself
# in editable mode with the full "all" feature set plus dev/test tooling.
# Copy the package source needed for an editable install.
COPY src ./src

RUN pip install --upgrade pip hatch uv \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
