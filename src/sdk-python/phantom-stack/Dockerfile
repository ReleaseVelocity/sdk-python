FROM python:3.13-slim

# System deps:
# - git: required by hatch-vcs to derive the version from git tags
# - build-essential: in case any dependency needs to compile native extensions
# - portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-install build backend so the editable install resolves quickly
RUN pip install --no-cache-dir --upgrade pip hatchling hatch-vcs

# Copy the full source tree
COPY . /app

# hatch-vcs needs a git repo with at least one tag to compute the version.
# Initialize a repo and create a baseline tag so the editable install succeeds
# even when build context has no .git directory.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "snapshot" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional deps,
# dev tooling, and bidi extras so tests, linters, and every command are usable.
RUN pip install --no-cache-dir -e ".[all,dev,bidi-all]"

CMD ["sleep", "infinity"]
