FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# System deps:
#  - git: required by hatch-vcs (version derived from git tags)
#  - gcc + portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata)
COPY src ./src

# hatch-vcs derives the version from git tags. The source tree has no .git
# in this environment, so initialise a throwaway repo with a tag so the
# build backend can resolve a version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL runtime extras
# plus dev/test tooling (pytest, ruff, mypy, moto, etc.)
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs)
COPY . .

# Re-run git add so newly copied files are tracked (version still v0.0.0)
RUN git add -A && git commit -qm "sources" || true

CMD ["sleep", "infinity"]
