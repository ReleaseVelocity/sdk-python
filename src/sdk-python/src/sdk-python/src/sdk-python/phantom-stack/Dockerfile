FROM python:3.13-slim

# System deps:
# - git: hatch-vcs derives the version from git tags/history at build time
# - portaudio19-dev / libasound2-dev: required to build pyaudio (bidi extra)
# - build-essential: occasional native builds for transitive deps
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
        libasound2-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project source.
COPY . /app

# hatch-vcs derives the package version from git. When building outside a git
# checkout (no .git present), provide a deterministic fallback so the editable
# install succeeds. setuptools_scm honors SETUPTOOLS_SCM_PRETEND_VERSION.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# Initialize a throwaway git repo with a tag if one is not already present so
# hatch-vcs can resolve a version reliably.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Default log level used by the test suite
ENV LOG_LEVEL=DEBUG

CMD ["sleep", "infinity"]
