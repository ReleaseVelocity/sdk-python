FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. There is no
# git history in this build context, so provide a pretend version instead.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# PyAudio (pulled in by the bidi extras) needs PortAudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# The package source is required because hatchling reads it during the editable
# install (and because we install in editable/dev mode).
COPY src ./src

# Install the project in editable/development mode with the full dependency set:
#   all      -> every model provider + a2a + otel + docs
#   bidi-all -> bidirectional streaming providers
#   dev      -> pytest, ruff, mypy, moto, hatch, etc.
RUN pip install -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
