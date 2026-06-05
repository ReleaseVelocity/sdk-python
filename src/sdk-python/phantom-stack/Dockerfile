FROM python:3.13-slim

# git is needed by hatch-vcs build backend; build-essential covers any
# optional native wheel builds.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree has no .git
# history, so provide a pretend version to make the editable install succeed.
# hatch-vcs delegates to setuptools-scm; the per-dist override is the reliable one.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in tests and the rest of the project.
COPY . .

CMD ["sleep", "infinity"]
