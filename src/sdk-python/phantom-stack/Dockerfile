FROM python:3.12-slim

# hatch-vcs (setuptools-scm) derives the version from git tags. A git repository
# with a tag is created below so the editable build can determine a version.
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git: needed by hatch-vcs / pre-commit
# build-essential: native builds if any wheel is unavailable
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project. hatch-vcs derives the package version from git, so we
# initialize a git repository with a tag to provide a valid version. This mirrors
# the real development workflow (a checked-out repo with tags).
COPY . .

RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "snapshot" && \
    git tag v0.0.0

# Install the project in editable/development mode with all dev + provider deps.
# "all" pulls every model provider extra; "dev" adds pytest, ruff, mypy, moto, etc.
# bidi extras are intentionally excluded (pyaudio/portaudio system deps) and the
# project's pytest config already ignores the bidi test paths.
RUN pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
