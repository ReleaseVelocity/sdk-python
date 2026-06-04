FROM python:3.13-slim

# hatch-vcs derives the version from git tags. This source tree has no git
# history, so provide a pretend version that hatch-vcs / setuptools-scm honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV HATCH_VCS_PRETEND_VERSION=1.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build tools occasionally needed for native wheels; keep minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata).
COPY src ./src

# Install the project in editable/development mode with all optional
# dependencies plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
