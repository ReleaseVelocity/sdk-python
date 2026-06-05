FROM python:3.12-slim

# System deps:
# - git: required because the project uses hatch-vcs (version derived from git tags)
# - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
# - build-essential: for any native wheels that need compilation
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        gcc \
        portaudio19-dev \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs derives the version from git tags. The source is not a git repo,
# so initialise a throwaway repo with a tag to give hatchling a version to read.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "containerize" && \
    git tag v1.0.0

# Upgrade pip tooling and install the project in editable/development mode
# with ALL optional dependencies and the dev/test toolchain.
RUN pip install --no-cache-dir --upgrade pip setuptools wheel && \
    pip install --no-cache-dir -e ".[all,bidi,bidi-gemini,bidi-openai,dev]"

CMD ["sleep", "infinity"]
