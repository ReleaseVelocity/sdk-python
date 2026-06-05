FROM python:3.13-slim

# Base dev image for the Strands Agents Python SDK.
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# PyAudio (bidi extra, pulled in by "all" via transitive paths) needs portaudio
# headers; git is useful for the hatch-vcs based tooling. Keep this minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the source package dir so the editable install metadata resolves
# during the dependency layer; full source is copied next.
RUN mkdir -p src/strands

# Copy the full source.
COPY . /app

# hatch-vcs (setuptools-scm) derives the version from git tags. The build
# context has no git history, so initialize a throwaway repo with a tag to
# satisfy the build backend in a way that works regardless of dist-name
# normalization quirks.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "dev build" && \
    git tag v0.0.0

# Install the project in editable/development mode with all runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
