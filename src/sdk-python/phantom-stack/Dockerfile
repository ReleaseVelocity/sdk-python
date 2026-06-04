FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the source tree may not
# include git history, provide a pretend version so installation succeeds.
ARG PKG_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${PKG_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# System deps: portaudio/alsa for pyaudio (bidi extra), plus build tooling
# for any packages that need to compile native extensions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because the install is editable and
# hatchling reads the package layout at build time).
COPY src ./src

# Install the project in editable/development mode with all optional features
# and the dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
