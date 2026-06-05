FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# pyaudio (bidi extra) needs PortAudio headers + a compiler.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for an editable install (hatchling reads packages at build).
COPY src ./src

# Install the project in editable/development mode with ALL optional deps,
# dev tooling, and the bidi extras (Python 3.12 supports bidi).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev,bidi-all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
