FROM python:3.11-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repo here, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Build tools are needed for a few native deps (e.g. pyaudio for bidi).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve before full source copy.
COPY src ./src

# Install the project in editable/dev mode with all optional features and dev/test deps.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
