FROM python:3.12-slim

# System deps:
# - git: required because hatch-vcs derives the package version from git tags
# - build-essential: in case any dependency needs native compilation
# - portaudio19-dev: pyaudio (bidi extra) needs portaudio headers if ever installed
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs a git repo with a tag to compute the version.
# Initialize a repo and create a baseline tag so the editable install succeeds.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "containerize baseline" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.)
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
