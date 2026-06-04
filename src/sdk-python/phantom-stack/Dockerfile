FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The build context may not
# carry git metadata, so provide a pretend version (consumed by setuptools_scm /
# hatch-vcs) and allow it to be overridden at build time.
ARG STRANDS_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is needed by hatch-vcs/setuptools_scm; build tools for any native deps.
RUN apt-get update && \
    apt-get install -y --no-install-recommends git build-essential && \
    rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / metadata generation).
COPY src ./src

# Install the project in editable/development mode with all runtime extras plus
# the dev + test tooling so a developer can run tests, linters, and type checks.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
