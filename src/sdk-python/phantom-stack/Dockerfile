FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context is not a git
# repository, so provide an explicit pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System libraries: portaudio is required to build/run pyaudio (bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata step succeeds
# before the full source is copied (improves cache reuse).
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install all runtime + optional + dev dependencies in editable mode.
RUN pip install -e ".[all,dev]"

# Copy the full project source.
COPY . .

# Re-run install to ensure entry points / metadata reflect the real package.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
