FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# metadata, so provide a pretend version to allow the editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Minimal system deps. PyAudio (bidi extra) needs portaudio headers; install them
# so optional extras can compile if ever needed. Kept small.
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because editable install references src/strands).
COPY src/strands ./src/strands

# Install the project in editable/development mode with all runtime extras plus
# the dev/test tooling declared in pyproject.toml.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

# Ensure pretend version persists for runtime tooling too.
CMD ["sleep", "infinity"]
