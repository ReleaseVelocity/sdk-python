FROM python:3.12-slim

# System deps:
# - git: required because hatch-vcs derives the package version from git tags
# - build-essential: in case any dependency needs to compile native extensions
# - portaudio19-dev: pyaudio (bidi extra) needs portaudio headers to build
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source
COPY . /app

# hatch-vcs requires a git repo with a tag to determine the version.
# The workspace is not a git repo, so create one with an initial tag.
# This is build-time only and does not modify the mounted source at runtime.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize" && \
    git tag v1.0.0

RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
