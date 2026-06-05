FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The source tree here is not a
# git checkout, so provide a pretend version (read by setuptools_scm which
# hatch-vcs is built on) to make the editable build succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# System deps:
# - git: build backend tooling expects it to be available
# - build-essential: a few transitive deps may need a compiler on slim
# - portaudio19-dev: required by pyaudio (bidi extra), harmless otherwise
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source and tests needed for the editable install + testing.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the SDK in editable/development mode with all optional model
# providers plus the full developer toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
