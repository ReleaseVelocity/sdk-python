FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There's no git history available
# in this build context, so provide a pretend version so the build/install works.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PYTHONUNBUFFERED=1

# pyaudio (pulled in by the optional "bidi" extra in [all]/[bidi-all]) needs
# portaudio headers and a C toolchain to compile.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        g++ \
        portaudio19-dev \
        libasound2-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the project installs in editable mode
# and hatchling reads packages = ["src/strands"]).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL runtime extras plus
# the dev + bidi tooling so a developer can run tests, linters, and type checks.
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
