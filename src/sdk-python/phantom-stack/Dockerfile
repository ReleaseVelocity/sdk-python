FROM python:3.12-slim

# Avoid interactive prompts and enable predictable Python behavior
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. There is no .git in this
# build context, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# git is occasionally needed by tooling; build tools kept minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable
# mode and hatchling reads packages = ["src/strands"]).
COPY src ./src

# Install the project in editable/development mode with all optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
