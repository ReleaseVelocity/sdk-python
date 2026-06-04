FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repo in this environment, so provide a fallback version for the build.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# System deps:
# - git: hatch-vcs build backend invokes git during metadata generation
# - portaudio19-dev + gcc: required to build pyaudio (pulled in by the bidi extra)
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        build-essential \
        portaudio19-dev \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Initialize a throwaway git repo with a tag so hatch-vcs can resolve a version,
# then install the project in editable/development mode with ALL extras + dev tools.
COPY . .
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git -c commit.gpgsign=false commit -qm "build" && \
    git tag v0.0.0 && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
