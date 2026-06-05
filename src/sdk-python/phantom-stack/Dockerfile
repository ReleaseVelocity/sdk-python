FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a pretend version so the
# build does not require git history/tags to be present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Minimal system deps. portaudio is needed by pyaudio (bidi extra); git is handy
# for tooling/pre-commit.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed for editable install since hatchling reads it).
COPY src ./src

# Install the project in editable/development mode with all optional features and
# dev/test tooling.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
