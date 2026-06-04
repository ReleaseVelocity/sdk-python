FROM python:3.10-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so provide a pretend version (setuptools_scm honored by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System dependencies. portaudio/alsa are required to build pyaudio (bidi extra),
# git is useful for tooling, build-essential covers any native compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution).
COPY src ./src

# Install the project in editable/development mode with dev + test dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
