FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Provide a fallback version so
# the editable install works even without git history/tags present.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# Minimal build tooling: git is needed by hatch-vcs, portaudio for the optional
# bidi/pyaudio extra (not installed here, but harmless to keep build lean).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so hatchling can resolve the editable install
# during the dependency-priming step.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project with all model providers + dev/test tooling in editable mode.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the full source tree.
COPY . .

CMD ["sleep", "infinity"]
