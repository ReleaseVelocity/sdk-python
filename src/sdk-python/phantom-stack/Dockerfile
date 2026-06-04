FROM python:3.12-slim

# Avoid interactive prompts and ensure clean Python output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs determines the version from git tags. There is no .git in the
# build context, so provide a pretend version so the build/install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev

# System deps:
# - git: hatch-vcs tooling; build-essential & portaudio for native builds
#   (pyaudio in bidi extras requires PortAudio headers)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for editable install metadata resolution.
COPY src ./src

# Install the project in editable/development mode with ALL optional deps,
# dev tooling, and test dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
