FROM python:3.12-slim

# Avoid interactive prompts; keep Python output unbuffered
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs determines the version from git tags. There is no git repo in the
# build context, so provide a pretend version so the build/install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System deps:
# - portaudio19-dev is needed to build pyaudio (a bidi optional dependency)
# - build-essential/gcc for any native wheels that lack prebuilt binaries
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata generation)
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain. The "all" extra requires native pyaudio only for
# the bidi extra, which we install separately to keep the core test suite green.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

# Keep the container alive for interactive development / exec
CMD ["sleep", "infinity"]
