FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This checkout has no .git
# directory, so we provide a pretend version (consumed by setuptools_scm,
# which hatch-vcs uses under the hood) to allow the build to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0.dev0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# portaudio is required to build/import pyaudio (pulled in by the bidi extra).
# git is kept available for developer workflows inside the container.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependencies. We copy the package source so an editable install
# can resolve the dynamic (vcs) version. The full source is overlaid afterwards.
COPY src ./src

# Install the SDK in editable/development mode with every optional feature
# group plus the dev + bidi tooling so a developer can run tests, linters,
# type-checks, and the full provider matrix inside the container.
RUN pip install --no-cache-dir -e ".[dev,all,bidi-all]"

# Bring in the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
