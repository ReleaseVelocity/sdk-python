FROM python:3.12-slim

# hatch-vcs derives version from git tags; no git repo is present in this build
# context, so provide a pretend version to let the build backend succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# PyAudio (pulled in by the optional 'bidi' extra) needs PortAudio headers, and
# a C toolchain is required for any deps without prebuilt wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and the build
# backend reads packages from src/strands).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras plus
# dev/test tooling and the bidi extras so the full test suite is collectable.
RUN pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
