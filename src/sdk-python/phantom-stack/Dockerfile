FROM python:3.12-slim

# Avoid interactive prompts and ensure clean python output
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives the version from git tags. The source tree here has no
# .git directory, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Minimal system deps. portaudio19-dev/gcc are needed only by the optional
# bidi extra (pyaudio); the core + [all] + [dev] install needs nothing beyond
# git (used by some tooling) and build basics for any sdist-only deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Layer caching: copy only the manifest first so dependency resolution is
# cached unless pyproject.toml changes.
COPY pyproject.toml README.md ./

# The wheel package lives under src/strands; create a placeholder so the
# editable install can resolve the package directory during the cached layer.
# (Full source is copied below for the actual editable install.)

# Copy the full project source.
COPY . .

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, mypy, ruff, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
