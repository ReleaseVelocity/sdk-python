FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide an explicit version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+docker \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Minimal system deps:
# - git: some tooling expects it; harmless and small
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with ALL optional deps,
# dev/test tooling, and bidirectional streaming extras.
RUN pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
