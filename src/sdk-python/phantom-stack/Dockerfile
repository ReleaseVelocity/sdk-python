FROM python:3.12-slim

# System deps: git is required by hatch-vcs for versioning; build tools for any
# native wheels (e.g. pyaudio for bidi extras needs portaudio).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs (setuptools-scm) derives the version from git tags. The source tree
# has no git metadata, so provide an explicit version to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=1.0.0
ENV HATCH_VCS_PRETEND_VERSION=1.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install build hook).
COPY src ./src

# Install the project in editable/dev mode with all optional + dev deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
