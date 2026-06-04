FROM python:3.13-slim

# hatch-vcs derives the version from git tags. This source tree has no git
# history/tags available, so provide a pretend version for the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# portaudio is required to build/use pyaudio (pulled in by the bidi extra).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
        libportaudio2 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source so the editable install can build the package.
COPY . .

# Install the project in editable/development mode with ALL optional features
# and the dev (test/lint) tooling.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
