FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The source tree has no
# .git directory, so we provide a pretend version to the build backend instead
# of requiring git history.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# git is occasionally needed by tooling; keep the image lean otherwise.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package directory so an editable install can resolve the
# project metadata before the full source tree is copied in.
RUN mkdir -p src/strands

# Copy the full source tree.
COPY . .

# Install the project in editable/development mode with ALL optional features
# and the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
