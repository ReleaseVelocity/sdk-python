FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# pyaudio (from the bidi extra) needs PortAudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        libc6-dev \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling, then install the project in editable/development mode
# with the full dependency set: all model providers, bidi, dev/test tooling.
RUN pip install --upgrade pip hatchling hatch-vcs hatch

# Copy the full source tree.
COPY . .

# Editable install with every optional dependency group used in development.
RUN pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
