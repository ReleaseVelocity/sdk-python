FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There's no git repo in this
# build context, so provide a pretend version so the build/install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs / general tooling
# - build-essential: in case any dependency needs to compile native code
# - portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install resolving packages).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test tooling so tests, linters and type checks
# can all be run from inside the container.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
