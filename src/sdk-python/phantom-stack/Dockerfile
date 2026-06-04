FROM python:3.12-slim

# Avoid interactive prompts and keep Python output unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The source tree shipped here is
# not a git checkout, so provide a pretend version that the build backend honors.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System build deps:
# - git: hatch-vcs reads it when present (harmless otherwise)
# - build-essential: some optional deps (e.g. audio/native wheels) may need a compiler
# - portaudio19-dev: required header set for pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed by the editable install / hatch build target.
COPY src/strands ./src/strands

# Install the project in editable/development mode with the full optional
# dependency matrix plus dev/test tooling.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
