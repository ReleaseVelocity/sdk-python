FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git repository, provide a pretend version so the editable install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# Default AWS region/credentials used by moto-backed unit tests.
ENV AWS_DEFAULT_REGION=us-west-2 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed by the editable install / hatchling).
COPY src ./src

# Install the project in editable/development mode with all optional features
# and the dev (test/lint) dependencies.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
