FROM python:3.12-slim

# Avoid interactive prompts and ensure unbuffered logs
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs (via setuptools-scm) derives the version from git tags. The build
# context has no .git folder, so provide a pretend version to satisfy the
# build backend. The normalized dist name for "strands-agents" is STRANDS_AGENTS.
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps: portaudio is required to build pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install / version detection.
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
