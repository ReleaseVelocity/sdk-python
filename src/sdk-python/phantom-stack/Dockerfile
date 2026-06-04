FROM python:3.12-slim

# System deps:
# - git: hatch-vcs reads version from git (we also set a pretend version as fallback)
# - portaudio19-dev: required to build pyaudio (bidi extras)
# - gcc/build-essential: native module compilation if needed
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools-scm) determines the version from git tags. There is no
# git history here, so provide a pretend version so the build/install does not fail.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source (needed for editable install to resolve the package)
COPY src ./src

# Install the project in editable/dev mode with ALL extras + dev/test tooling.
# bidi-all includes pyaudio and python_version>=3.12 only deps.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all,bidi-all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
