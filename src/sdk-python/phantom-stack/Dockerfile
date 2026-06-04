FROM python:3.12-slim

# Version is normally derived from git tags via hatch-vcs.
# Source tree has no .git, so provide a pretend version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Build/runtime libs: pyaudio (bidi extra) needs portaudio + gcc.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-resolve deps using the manifest before full source is present.
# Editable install requires the package dir, so create a stub then full copy.
COPY src ./src

# Install the project in editable/dev mode with all + dev dependencies.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the source (tests, configs, etc.)
COPY . .

CMD ["bash"]
