FROM python:3.13-slim

# hatch-vcs determines version from git tags. There is no .git here at build,
# so provide a pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra) needs portaudio dev headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies (editable install of just the project metadata).
# Source is copied next; full editable install happens after.
COPY src/ ./src/

# Install the project in editable/dev mode with all optional features and dev tooling.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
