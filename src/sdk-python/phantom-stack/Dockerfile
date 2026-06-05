FROM python:3.13-slim

# hatch-vcs derives the version from git tags. This checkout is not a git repo,
# so provide a pretend version for setuptools_scm/hatch-vcs.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio) need portaudio/build tooling. The "bidi"
# extras are excluded from the default test run, but install build tools to be
# safe for native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install / version resolution.
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
# Exclude "bidi" extras here (they require python>=3.12-only packages and audio
# stacks not needed for the default test suite), but the project's [all] and
# [dev] cover the full default test matrix.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
