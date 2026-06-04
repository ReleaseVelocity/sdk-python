FROM python:3.13-slim

# Native build deps: portaudio for pyaudio (bidi extra), git for hatch-vcs, gcc for any C extensions
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        portaudio19-dev \
        libsndfile1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source here has no .git,
# so provide a pretend version to satisfy the dynamic version build.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install)
COPY src ./src

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, hatch, etc.)
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
