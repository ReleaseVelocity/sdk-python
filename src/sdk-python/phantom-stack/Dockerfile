FROM python:3.12-slim

# System deps: build tools for any native wheels (e.g. pyaudio) + portaudio + git
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        gcc \
        git \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs determines version from git tags; there is no git repo here,
# so provide a pretend version to allow the build to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata
COPY src ./src

# Install the project in editable/development mode with ALL optional deps + dev/test tooling
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
