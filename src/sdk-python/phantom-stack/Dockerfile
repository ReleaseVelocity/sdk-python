FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree shipped here is
# not a git checkout, so provide a pretend version for setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling. Most wheels are prebuilt; gcc is kept available in case
# a transitive dependency needs to compile a small native extension.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
        gcc \
        build-essential && \
    rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable build backend can resolve packages
# during the dependency-only install step.
COPY src/ ./src/

# Install the project in editable/development mode with the full feature set and
# the dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

# Default to an interactive shell so a developer can exec in and run commands.
CMD ["sleep", "infinity"]
