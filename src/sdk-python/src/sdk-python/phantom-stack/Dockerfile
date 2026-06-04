FROM python:3.12-slim

# Avoid interactive prompts; predictable Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so provide a pretend version to allow the build to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# pyaudio (from the bidi extra) needs the PortAudio headers to build.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libffi-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install / metadata).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
