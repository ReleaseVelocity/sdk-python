FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history/tags, so provide an explicit version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is occasionally needed by hatch-vcs / pre-commit tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and hatchling
# reads the package metadata/files at build time).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain so a developer can run tests, linters, and type
# checks inside the container.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
