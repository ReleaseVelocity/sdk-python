FROM python:3.11-slim

# hatch-vcs derives the version from git tags. The source tree here is not a git
# checkout, so provide an explicit pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# portaudio19-dev is required if the optional bidi extra (pyaudio) is ever built.
# build-essential covers any source-only wheels in the dependency tree.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling references src/strands at build time).
COPY src ./src

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling (pytest, moto, ruff, mypy, etc).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
