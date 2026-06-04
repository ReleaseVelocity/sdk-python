FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the source is shipped
# without a .git directory, provide a fallback version so the build/install
# does not fail. Override at build time with --build-arg if needed.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    HATCH_BUILD_NO_HOOKS=false \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps:
# - git: hatch-vcs/setuptools_scm probing; also useful for dev workflows
# - build-essential: in case any dependency needs to compile a native wheel
# - portaudio19-dev: required by pyaudio (bidi extra) should it be installed
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
        jq \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
