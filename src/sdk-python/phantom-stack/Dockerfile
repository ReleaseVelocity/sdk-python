FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no git
# history, so provide a pretend version (consumed by setuptools_scm, which
# hatch-vcs wraps) to allow the editable build to succeed.
ARG PRETEND_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PRETEND_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Minimal system deps:
# - git: hatch-vcs / general dev tooling
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra) if pulled in
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install runtime + all optional providers + dev/test tooling in editable mode.
# We pre-create the package dir so the editable install resolves before the
# full source copy (then the real sources overwrite it).
RUN mkdir -p src/strands && touch src/strands/__init__.py \
    && pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project source.
COPY . .

# Re-run the editable install now that full sources (including extra package
# metadata) are present; quick because deps are already satisfied.
RUN pip install -e ".[all,dev]"

CMD ["bash"]
