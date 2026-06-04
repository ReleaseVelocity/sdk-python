FROM python:3.12-slim

# Avoid interactive prompts and enable faster, cleaner pip behavior
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    DEBIAN_FRONTEND=noninteractive

# hatch-vcs derives the version from git tags. There is no .git in the build
# context, so provide a pretend version so the build/install succeeds.
# hatch-vcs delegates to setuptools-scm, which reads SETUPTOOLS_SCM_PRETEND_VERSION.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# System deps:
# - git: required by hatch-vcs / general dev usage
# - portaudio19-dev + gcc/build tools: pyaudio (bidi extra) needs to compile
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        make \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

# Install dependencies first using only the manifest for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve/install dependencies (editable target installed again after full
# source copy). This warms the cache for heavy deps.
RUN pip install --upgrade pip

# Copy the full project source.
COPY . .

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
