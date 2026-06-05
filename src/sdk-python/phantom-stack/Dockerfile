FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to the setuptools-scm-compatible backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# PyAudio (bidi extra) needs portaudio headers; git kept for tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata can resolve.
COPY src ./src

# Install the project in editable/development mode with all dependencies,
# dev/test tooling, and optional integrations.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the source tree.
COPY . .

CMD ["sleep", "infinity"]
