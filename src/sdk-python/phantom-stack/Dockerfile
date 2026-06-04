FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# repository, so provide a pretend version to satisfy the dynamic version hook.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is required by hatch-vcs at build time even with a pretend version.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with ALL extras plus dev
# and test tooling so a developer can run tests, linters, and type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
