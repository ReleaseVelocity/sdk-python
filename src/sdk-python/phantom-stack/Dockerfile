FROM python:3.12-slim

# pyaudio (bidi) is not part of [all]; no native build tools needed for [dev,all].
# Install minimal build tooling only if a dependency needs compiling.
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# hatch-vcs derives the version from git tags. There is no .git here, so we
# provide a pretend version (setuptools-scm/hatch-vcs honors this env var).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src/ ./src/

# Editable install with all optional model providers + dev/test tooling.
RUN pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
