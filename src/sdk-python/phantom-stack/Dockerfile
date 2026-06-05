FROM python:3.13-slim

# hatch-vcs (setuptools_scm) needs a version. Since the source tree is not a
# git checkout in this environment, provide a pretend version so the editable
# install can determine the package version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is referenced by hatch-vcs; install a minimal toolchain.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve packages).
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
