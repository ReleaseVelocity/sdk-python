FROM python:3.11-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# editable install succeeds even without a populated .git directory.
ARG PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs/hatchling at build time; build-essential helps any
# packages that need to compile native extensions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and hatchling
# reads packages = ["src/strands"]).
COPY src ./src

# Install the project in editable/development mode with dev tools and all
# optional model-provider extras so any project command (tests, ruff, mypy) works.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
