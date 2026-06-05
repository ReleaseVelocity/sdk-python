FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The source tree here is
# not a git repo, so we make git available and provide a pretend version so the
# editable install resolves a concrete version without needing real tags.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    AWS_DEFAULT_REGION=us-west-2

# git: needed by hatch-vcs / hatchling build backend at install time.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Bring in the package source needed for the editable build target.
COPY src/strands ./src/strands

# Initialize a throwaway git repo + tag so hatch-vcs can compute a version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "snapshot" \
    && git tag v0.0.0

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, pytest-asyncio, pytest-xdist, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
