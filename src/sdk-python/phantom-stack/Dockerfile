FROM python:3.13-slim

# System deps:
# - git: required by hatch-vcs to derive the package version from tags
# - build-essential: in case any dependency needs native compilation
# - portaudio19-dev: required to build pyaudio (pulled in by the bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so we provide a pretend version to keep the editable install reproducible.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install to resolve packages).
COPY src ./src

# Initialise a git repo with a tag so hatch-vcs can resolve a version,
# then install the project in editable/development mode with all dependency
# groups (runtime extras + dev/test tooling).
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "containerize" && \
    git tag v0.0.0 && \
    pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[dev,all]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
