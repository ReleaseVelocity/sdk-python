FROM python:3.13-slim

# Install git (needed by hatch-vcs to determine version) and build basics
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags. The source tree may not contain
# git history, so provide a fallback version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1

# Copy the full source
COPY . .

# Initialise a git repo if none exists so hatch-vcs has something to read,
# then install the project in editable/development mode with ALL extras + dev tools.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi && \
    pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
