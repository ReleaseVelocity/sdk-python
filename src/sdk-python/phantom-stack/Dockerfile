FROM python:3.12-slim

# Avoid interactive prompts and ensure clean Python output
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. This source tree has no git
# metadata, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Minimal build tooling. The base dependency set is pure-python wheels, but
# git is handy for hatch-vcs and dev workflows.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so an editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with every optional feature
# group plus the dev/test tooling so a developer can run tests and linters.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
