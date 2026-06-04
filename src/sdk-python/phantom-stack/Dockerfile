FROM python:3.13-slim

# hatch-vcs (setuptools-scm) derives the version from git tags. The build
# context has no .git, so provide a pretend version to allow editable installs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; pure-python deps generally need no compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because editable install references it).
COPY src ./src

# Install the project in editable/development mode with ALL extras + dev/test deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
