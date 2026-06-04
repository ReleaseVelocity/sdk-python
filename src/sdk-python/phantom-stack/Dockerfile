FROM python:3.12-slim

# System deps: portaudio for pyaudio (bidi extra), git for tooling
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools-scm) determines version from git tags; no repo here, so pretend a version
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source (needed for editable install / hatch build backend)
COPY src ./src

# Install project in editable/development mode with all + dev dependencies
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
