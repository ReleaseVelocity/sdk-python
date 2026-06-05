FROM python:3.12-slim

# Build args for git versioning (hatch-vcs determines version from git tags)
ARG SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION}
# hatch-vcs uses this env var to avoid requiring a git tag
ENV HATCH_VCS_PRETEND_VERSION=${SETUPTOOLS_SCM_PRETEND_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# System deps:
#  - git: hatch-vcs reads version from git metadata
#  - portaudio19-dev: required by pyaudio (pulled in by bidi extras, but
#    we keep it available so the full optional set can be installed if needed)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source needed for an editable install
COPY . .

# Initialize a git repo so hatch-vcs has metadata to work with, then
# install the project in editable/dev mode with ALL extras plus dev/test tools.
RUN git init -q . 2>/dev/null || true; \
    git config user.email "dev@example.com" 2>/dev/null || true; \
    git config user.name "dev" 2>/dev/null || true; \
    pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
