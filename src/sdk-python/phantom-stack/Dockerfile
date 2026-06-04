FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here has no
# .git directory, so provide an explicit version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# pyaudio (from the optional "bidi" deps, pulled via test extras) needs PortAudio
# headers + a compiler. Install build tooling for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with all runtime extras,
# dev tooling, and bidi extras so every project command is available.
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
