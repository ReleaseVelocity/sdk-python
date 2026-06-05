FROM python:3.12-slim

# Version is normally derived from git tags via hatch-vcs. Since the build
# context has no git history, provide a pretend version so the build succeeds.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tools for any optional native deps; portaudio for pyaudio (bidi).
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so an editable install has something to link against.
COPY src/strands ./src/strands

# Install the project in editable/development mode with all features + dev tools.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["python", "-c", "import strands; print('strands', strands.__name__, 'ready')"]
