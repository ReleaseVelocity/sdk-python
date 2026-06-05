FROM python:3.13-slim

# hatch-vcs (setuptools_scm) needs a version; no .git present in the build context,
# so provide a pretend version. Overridable at build time.
ARG STRANDS_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by the optional bidi extra,
# though bidi itself only installs on py>=3.12). Keep build deps minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve/install dependencies using the manifest + source package.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ
COPY docs ./docs

# Editable install with all optional features + dev/test tooling.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
