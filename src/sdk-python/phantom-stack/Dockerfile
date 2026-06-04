FROM python:3.12-slim

# Avoid interactive prompts; ensure unbuffered output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is needed because hatch-vcs derives the version from git tags/history.
RUN apt-get update && \
    apt-get install -y --no-install-recommends git && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source tree (package, tests, git metadata).
COPY . .

# hatch-vcs needs a version. If no git tags exist, provide a fallback so the
# editable install succeeds regardless of the repo's tag state.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with ALL runtime extras
# plus dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN python -m pip install --upgrade pip && \
    pip install -e ".[all]" -e ".[dev]"

CMD ["sleep", "infinity"]
