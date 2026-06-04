FROM python:3.12-slim

# hatch-vcs (setuptools_scm) determines version from git tags. Since the build
# context may not contain git metadata, provide a fallback pretend version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed for hatch-vcs version detection / general dev use.
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install can resolve the build.
# Copy the full source.
COPY . /app

# Install the project in editable/development mode with all optional features
# and the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
