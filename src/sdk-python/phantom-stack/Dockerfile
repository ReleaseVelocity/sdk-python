FROM python:3.13-slim

# hatch-vcs (setuptools-scm based) derives the version from git tags.
# This repo is shipped without a .git directory, so we provide a pretend version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is occasionally needed by build backends / pre-commit; build tools for
# any native wheels that lack prebuilt binaries.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution).
COPY src ./src

# Install the project in editable/development mode with all optional groups
# plus the dev/test tooling so a developer can run tests and linters.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all,bidi-all]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
