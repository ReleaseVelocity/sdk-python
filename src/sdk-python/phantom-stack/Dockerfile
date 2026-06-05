FROM python:3.12-slim

# hatch-vcs/setuptools_scm needs a version when no .git is present
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps:
#  - git: hatch-vcs may shell out to git
#  - portaudio19-dev: required to build pyaudio (bidi extras)
#  - build-essential: native builds (pyaudio, etc.)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata step can resolve packages
COPY src/ ./src/

# Install the project in editable/dev mode with all extras + dev/test tooling.
# 'all' pulls every model provider so unit tests can import them.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
