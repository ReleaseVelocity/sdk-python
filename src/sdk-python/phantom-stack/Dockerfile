FROM python:3.13-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    LOG_LEVEL=DEBUG

# System deps:
# - git: required by hatch-vcs (version derived from git tags)
# - build-essential: in case any dependency needs to compile a native extension
# - portaudio19-dev / libasound2-dev: needed by pyaudio (bidi extra), matches CI
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git history; the source tree has no .git,
# so create a minimal repo with a tag to give it a concrete version.
COPY src ./src
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v0.0.0

# Install the project in editable/dev mode with all optional features plus
# the developer + test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
