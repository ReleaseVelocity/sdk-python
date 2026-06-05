FROM python:3.12-slim

# Version cannot be derived from git tags (not a git checkout in this env),
# so provide it explicitly to hatch-vcs / setuptools-scm.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# System deps:
# - git: hatch-vcs build backend probes for git
# - build-essential: in case any dependency needs to compile
# - portaudio19-dev / libasound2-dev: required by pyaudio (bidi extra)
#   so the full "all"/dev install does not fail
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create package dir so editable install metadata resolves before full source copy
RUN mkdir -p src/strands

# Copy full source
COPY . /app

# Install the project in editable/development mode with ALL feature extras + dev tooling
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
