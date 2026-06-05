FROM python:3.12-slim

# Keep Python output unbuffered and avoid .pyc clutter
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# git is generally useful for dev tooling (pre-commit, etc.).
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# The build backend (hatch-vcs) normally derives the version from git tags.
# This source tree has no .git directory, so provide an explicit version.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source tree and tests needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
