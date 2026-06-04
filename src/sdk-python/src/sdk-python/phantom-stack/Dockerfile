FROM python:3.13-slim

# hatch-vcs derives the version from git tags. There is no git history in the
# build context, so provide an explicit version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling. Most wheels are prebuilt; build-essential covers any
# source-only deps that need compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata).
COPY src/strands ./src/strands

# Install the project in editable/development mode with all model-provider
# extras plus dev/test tooling.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
