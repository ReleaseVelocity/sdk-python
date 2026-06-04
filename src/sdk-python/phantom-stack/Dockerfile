FROM python:3.12-slim

# Avoid interactive prompts and enable faster, cleaner pip
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The source tree provided is not a
# git repository, so we supply a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System deps:
#  - git: hatch-vcs version backend probes for git metadata
#  - portaudio19-dev + gcc: needed if the bidi/pyaudio extras are ever built
WORKDIR /app

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Leverage layer caching: copy only the manifest first.
COPY pyproject.toml README.md ./

# Copy the package source so an editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras plus
# the dev/test toolchain (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
