FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# be a git repository, provide a pretend version so the build succeeds.
ARG STRANDS_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV HATCH_VCS_PRETEND_VERSION=${STRANDS_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed by hatch-vcs; build tools occasionally needed for native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves; the
# real sources are copied right after.
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional deps and
# the dev/test toolchain (pytest, moto, mypy, ruff, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
