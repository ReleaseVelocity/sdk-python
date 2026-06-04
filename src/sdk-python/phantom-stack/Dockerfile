FROM python:3.12-slim

# hatch-vcs derives the package version from git tags, which are not available
# inside the build context. Provide a pretend version so the editable install
# (and the dynamic `version` field) resolves without git metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build tooling. portaudio19-dev is needed for the optional `bidi`
# extra (pyaudio); included so the full dependency set installs cleanly.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        portaudio19-dev \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching, then install all runtime,
# optional (all + bidi) and development/test dependencies in editable mode.
COPY pyproject.toml README.md ./
COPY src/strands/__init__.py src/strands/__init__.py

RUN pip install --upgrade pip \
    && pip install -e ".[all,bidi-all,dev]"

# Now copy the full source tree (changes here don't bust the dep-install layer).
COPY . .

# Re-install editable to pick up the full package metadata/entrypoints.
RUN pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
