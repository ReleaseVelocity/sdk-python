FROM python:3.12-slim

# pyaudio (bidi extra) needs portaudio headers; git needed in case tooling expects it.
# Keep build tools minimal; install only if native builds require them.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide an explicit version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src/ ./src/

# Install the project in editable/development mode with all optional + dev deps.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.)
COPY . .

CMD ["sleep", "infinity"]
