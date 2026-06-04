FROM python:3.13-slim

# hatch-vcs derives the version from git tags. There is no git history in the
# build context, so provide a pretend version (overridable at build time).
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PKG_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps: portaudio is required to build pyaudio (pulled in by bidi extras).
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata resolution.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
