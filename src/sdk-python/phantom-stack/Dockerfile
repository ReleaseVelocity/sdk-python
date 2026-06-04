FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory here, so provide a fallback version that hatch-vcs (setuptools_scm
# compatible) will honor instead of failing the build/install.
ARG PRETEND_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Minimal system deps. portaudio is required to build pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with all extras + dev tools.
# Exclude the bidi extras (require portaudio/pyaudio system libs and python>=3.12
# native sdk) which are not part of the default test suite (ignored in pytest config).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
