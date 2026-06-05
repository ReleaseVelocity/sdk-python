FROM python:3.12-slim

# hatch-vcs (setuptools_scm) reads the version from git tags. The build context
# has no .git directory, so we provide a pretend version to satisfy the backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# System deps:
# - build-essential: in case any dependency needs to compile
# - portaudio19-dev: required to build pyaudio (used by the optional `bidi` feature)
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package directory so editable install metadata resolves,
# then install all runtime + optional + dev dependencies.
COPY src/ ./src/

# Install the project in editable/development mode with ALL extras and dev tools.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
