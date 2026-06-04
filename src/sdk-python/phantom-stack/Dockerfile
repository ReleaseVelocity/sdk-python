FROM python:3.12-slim

# System deps:
# - git: required by hatch-vcs to compute the dynamic version from git tags
# - build-essential: in case any dependency needs to compile native extensions
# - portaudio19-dev: needed by pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed because the package is installed editable
# and hatch-vcs reads git metadata for the version).
COPY . .

# hatch-vcs needs a git repo with at least one tag/commit to derive a version.
# If the build context isn't a git checkout, initialize a throwaway repo so the
# editable install can compute a version.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL optional features
# and the dev/test tooling so a developer can run tests, linters, and type checks.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
