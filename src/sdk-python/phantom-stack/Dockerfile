FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree may not contain
# git history, so provide a pretend version so the build/install can succeed.
ARG STRANDS_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# System deps: git (hatch-vcs), and build tooling only if a wheel needs it.
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip hatch

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve dependencies (editable install needs the package files too, so
# the real install happens after sources are copied; this layer just warms pip).
# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with ALL optional deps and
# the dev/test toolchain so a developer can run tests, linters, and type checks.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
