FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version; no .git present in build context,
# so provide a pretend version. Can be overridden at build time.
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}

WORKDIR /app

# Minimal system deps. portaudio is needed for pyaudio (bidi extra); keep build lean otherwise.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source so editable install can resolve the package.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install in editable/dev mode with all optional features plus dev/test tooling.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
