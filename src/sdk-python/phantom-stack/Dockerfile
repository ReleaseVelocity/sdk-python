FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs derives the version from git tags. The build context has no git
# repo, so provide a pretend version to satisfy the hatchling build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev

# Minimal build tooling. pyaudio (bidi extra) needs portaudio headers; install
# them so the full dependency set can build if required.
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package directory so hatchling can resolve the wheel target
# during the editable install of just the manifest layer.
RUN pip install --upgrade pip

# Copy the full source.
COPY . /app

# Install the project in editable/development mode with all optional features
# plus the dev (test/lint) tooling.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
