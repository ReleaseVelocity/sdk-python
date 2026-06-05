FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so provide a pretend version to allow the build to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build pyaudio (pulled in by the bidi extra deps).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because editable install references src/strands).
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
