FROM python:3.12-slim

# Avoid interactive prompts and enable predictable Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Build tooling: git is needed because hatch-vcs derives the version from git
# tags; build-essential covers any native wheels that lack manylinux builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs needs a git repo (or a pretend version) to build. We provide the
# pretend version via env vars above, but initialize a repo so editable
# install metadata resolves cleanly.
RUN git init -q && git add -A && git -c user.email=ci@local -c user.name=ci commit -qm init || true

# Copy the source so the editable install has packages to link against.
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
