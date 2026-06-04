FROM python:3.12-slim

# Avoid interactive prompts and ensure clean Python runtime behavior
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs/setuptools_scm derives the version from git tags. The source tree
# here may not have tags, so provide a pretend version so the build succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps:
# - git: required by hatch-vcs build backend
# - build-essential: in case any dependency needs to compile native extensions
# - portaudio19-dev/libasound2-dev: parity with CI for the optional bidi (pyaudio) deps
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev && \
    rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads packages = ["src/strands"])
COPY src ./src

# Install the project in editable/development mode with all runtime extras and
# the dev/test toolchain so a developer can run tests, linters, and type checks.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
