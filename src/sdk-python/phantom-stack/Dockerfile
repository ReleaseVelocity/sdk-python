FROM python:3.11-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# be a git repository, provide a pretend version so the build succeeds.
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV HATCH_VCS_PRETEND_VERSION=${PKG_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Build/runtime libs occasionally needed by transitive deps (e.g. pyaudio for bidi).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads the package during the build).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, mypy, ruff, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
