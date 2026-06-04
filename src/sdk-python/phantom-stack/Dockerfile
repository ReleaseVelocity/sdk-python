FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This checkout has no .git/tags,
# so provide a pretend version that hatch-vcs/setuptools-scm honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# pyaudio (bidi extra) needs portaudio headers; build tools for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable install metadata resolves during the
# manifest-only layer (full source is copied below).
RUN mkdir -p src/strands

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
COPY src ./src
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
