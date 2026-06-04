FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. Since the
# source tree may be shipped without .git history, provide a pretend version.
# Overridable via build arg so different environments can supply their own.
ARG SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Build/runtime deps. portaudio for the optional pyaudio (bidi) builds; git in
# case version detection needs it.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy dependency manifest first.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL optional features
# plus dev/test tooling. The package source isn't present yet, so install deps
# only here would fail (editable needs the package); instead copy src too.
COPY src ./src

# Editable install with every optional dependency group + dev tooling so a
# developer can run tests, linters, type checks, etc.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
