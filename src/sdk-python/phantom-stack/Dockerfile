FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version requirement.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# pyaudio (from the bidi extra) needs portaudio system headers; install build
# tools and audio libs so optional native deps compile cleanly.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves during
# the dependency-only layer; full source is copied next.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project in editable/development mode with all extras plus the
# dev (test/lint) tooling.
RUN pip install -e ".[all,dev]"

# Copy the full project source.
COPY . .

# Reinstall editable to pick up the real package metadata/entry points.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
