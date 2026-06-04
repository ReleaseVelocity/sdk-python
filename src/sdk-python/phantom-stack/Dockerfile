FROM python:3.13-slim

# hatch-vcs / setuptools-scm derive the version from git tags. The source tree
# in this environment is not a git checkout, so provide an explicit fallback
# version to make the editable install succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is required by hatch-vcs build hooks; build-essential covers any source
# builds that may be needed by transitive dependencies.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install metadata build).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional features
# plus the dev/test tooling so a developer can run tests, linters, type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
