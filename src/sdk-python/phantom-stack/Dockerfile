FROM python:3.12-slim

# hatch-vcs derives version from git tags; this repo has no git history in the
# build context, so provide a pretend version (consumed by setuptools_scm backend).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Some optional deps (e.g. pyaudio in bidi) need build/audio headers; install
# minimal build tooling so editable install with [all] succeeds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
        g++ \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for layer caching.
COPY pyproject.toml README.md ./

# Initialize a git repo so hatch-vcs can resolve a version, then install all
# runtime + optional + dev/test dependencies in editable (development) mode.
COPY src ./src
RUN git init -q && git add -A && git -c user.email=dev@example.com -c user.name=dev commit -qm init \
    && git tag v0.0.0 \
    && pip install -e ".[all,dev]"

# Copy the remaining project sources (tests, configs, docs).
COPY . .

CMD ["bash"]
