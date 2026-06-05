FROM python:3.12-slim

# hatch-vcs derives the version from git tags; provide a fallback so the
# editable install works even without full git history available.
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# System deps:
# - git: required by hatch-vcs build backend
# - build-essential: for any source builds of transitive deps
# - portaudio19-dev: required to build pyaudio (bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because pyproject is configured for an
# editable/VCS build and references src/strands).
COPY src ./src

# Initialize a git repo so hatch-vcs can resolve a version, then install the
# project in editable/development mode with ALL optional + dev dependencies.
RUN git init -q && git add -A && \
    git -c user.email=dev@example.com -c user.name=dev commit -qm "init" && \
    git tag v0.0.0 && \
    pip install --upgrade pip && \
    pip install -e ".[all,dev,bidi,bidi-gemini,bidi-openai]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
