FROM python:3.12-slim

# Build arg for hatch-vcs version (project derives version from git tags).
# Provide a pretend version so the build does not require a populated git history.
ARG HATCH_VCS_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${HATCH_VCS_PRETEND_VERSION}

# System deps:
# - git: hatch-vcs version source
# - build-essential: native builds for some optional deps
# - portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatchling needs the package source present to build the editable install,
# and hatch-vcs needs a git repo. Initialize a lightweight repo + tag so
# version resolution succeeds deterministically.
COPY src ./src

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "init" && \
    git tag v${HATCH_VCS_PRETEND_VERSION}

# Install the project in editable/dev mode with all optional features + dev tools.
RUN pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
