FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repository, so provide a pretend version to satisfy the build backend.
# (hatch-vcs honors the setuptools-scm pretend version env vars.)
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# portaudio is needed for pyaudio (bidi extra); build tools for native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Install all runtime, optional, and dev dependencies in editable mode.
# Copy the rest of the source (needed because editable install references src/).
COPY . .

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
