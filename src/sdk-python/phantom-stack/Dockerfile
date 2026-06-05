FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so we provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps:
# - git: hatch/hatch-vcs tooling may probe for it
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        libc6-dev \
        portaudio19-dev \
        jq \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the actual package source so the editable install can resolve it.
COPY src/strands ./src/strands

# Install the project in editable/development mode with all runtime extras,
# the bidi extras (audio/realtime), and the dev/test tooling.
RUN pip install -e ".[all,bidi-all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
