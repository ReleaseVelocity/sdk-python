FROM python:3.13-slim

# hatch-vcs derives the version from git tags. There is no .git in this
# build context, so provide a pretend version (honored by setuptools-scm /
# hatch-vcs) to allow an editable install without git history.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build deps occasionally needed by transitive packages (e.g. pyaudio not in
# default extras, but keep portaudio out unless needed). Install git so any
# tooling expecting a repo behaves, plus basic build tools for native wheels.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves; the
# real source is mounted/copied below.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# providers plus the dev & test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Default to an interactive shell so developers can exec in and run tooling.
CMD ["bash"]
