FROM python:3.12-slim

# System deps:
#  - git: required by hatch-vcs to derive the package version from git tags
#  - portaudio19-dev + gcc: required to build pyaudio (bidi extra)
#  - build-essential: for any native deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        gcc \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs needs a git repo with at least one tag/commit to compute the version.
# Initialize a throwaway repo and tag so editable install resolves a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "bootstrap" \
    && git tag v0.0.0

# Copy the source so the editable install has the package available.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Re-commit full source so hatch-vcs sees a clean tree (version = v0.0.0).
RUN git add -A && git commit -qm "source" && git tag -f v0.0.0

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,bidi-all,dev]"

CMD ["sleep", "infinity"]
