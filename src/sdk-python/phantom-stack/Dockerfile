FROM python:3.13-slim

# hatch-vcs (backed by setuptools-scm) derives the package version from git
# tags. The build context has no git history, so provide a pretend version to
# let the editable install succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

WORKDIR /app

# System deps: git is referenced by hatch-vcs build backend; build tools kept
# minimal and only added if native compilation is required.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git curl \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install resolving packages).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
