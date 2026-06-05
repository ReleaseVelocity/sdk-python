FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git metadata in the
# build context, so provide a pretend version to let the build succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is occasionally needed by tooling; build tools kept minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads the package tree).
COPY src ./src

# Install the project in editable/development mode with ALL optional model
# providers plus the dev/test toolchain.
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
