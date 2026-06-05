FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git history available
# in this build context, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal build tooling: git is referenced by hatch-vcs, build-essential covers
# any optional native wheels that lack prebuilt distributions.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with all optional features
# and the dev (test/lint) dependency group.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the remaining project files (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
