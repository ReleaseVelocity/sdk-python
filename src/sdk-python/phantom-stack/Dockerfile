FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to allow the editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install to resolve packages).
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
