FROM python:3.12-slim

# Avoid interactive prompts and ensure UTF-8
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# System deps:
#  - git: hatch-vcs derives the version from git tags/history
#  - build-essential: in case any dependency needs to compile native extensions
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install) and tests
COPY src ./src

# The project uses hatch-vcs (git tags) for versioning. The build context is
# not a git repo here, so provide the version explicitly to hatchling.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Upgrade pip tooling and install the project in editable/development mode
# with all optional model-provider extras plus the dev/test toolchain.
RUN python -m pip install --upgrade pip setuptools wheel \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
