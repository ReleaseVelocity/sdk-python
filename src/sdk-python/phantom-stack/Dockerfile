FROM python:3.13-slim

# hatch-vcs/setuptools-scm normally derive the package version from git tags.
# The source tree is not a git repository here, so we supply an explicit version
# to the build backend, which also removes any need for git during the build.
ENV HATCH_VCS_PRETEND_VERSION=1.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with all optional features
# and the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
