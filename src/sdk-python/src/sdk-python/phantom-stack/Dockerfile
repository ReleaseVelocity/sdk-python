FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to allow the build backend to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# PyAudio (bidi extra) needs PortAudio headers; build tooling for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install project in editable/dev mode with all optional + dev dependencies.
# Copy the full source needed for editable install metadata.
COPY src ./src

RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
