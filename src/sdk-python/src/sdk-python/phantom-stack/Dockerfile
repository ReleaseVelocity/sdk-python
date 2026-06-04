FROM python:3.12-slim

# System deps:
#  - git: hatch-vcs derives the version from git history/tags at build time
#  - build-essential: in case any wheel needs compilation
#  - portaudio19-dev: required to build pyaudio (bidi optional dependency)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# The project uses hatch-vcs for versioning. The source tree is not a git
# repository, so we provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Copy the rest of the source
COPY . .

# Initialize a git repo with a version tag so hatch-vcs can resolve a version
# (covers both the pretend-version path and any direct git introspection).
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "build" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,bidi,bidi-gemini,bidi-openai,dev]"

CMD ["sleep", "infinity"]
