FROM python:3.11-slim

# Avoid interactive prompts and reduce noise
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Install git (needed by hatch-vcs to derive the version) and minimal build deps.
# pyaudio (bidi extra) needs portaudio headers; build-essential covers any native builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source tree.
COPY . /app

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory, so initialize a throwaway repo with a tag to satisfy versioning.
RUN git init -q && \
    git config user.email "build@example.com" && \
    git config user.name "build" && \
    git add -A && \
    git commit -qm "containerization build" && \
    git tag v0.0.0

# Upgrade pip and install the project in editable/development mode with ALL
# optional dependencies plus the dev/test tooling.
RUN python -m pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
