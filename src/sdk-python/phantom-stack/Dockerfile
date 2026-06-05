FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source is provided
# without git metadata, tell setuptools-scm/hatch-vcs to use a fixed version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by some build/dev tooling; build-essential covers any
# native extension builds in the (large) dependency set.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install all runtime extras + dev/test tooling in editable mode.
# Copy the source needed for the editable install before installing.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
