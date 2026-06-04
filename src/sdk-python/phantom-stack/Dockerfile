FROM python:3.12-slim

# Allow hatch-vcs to determine version from git; provide a fallback if no tags
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs to compute the dynamic version
RUN apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the project (source, tests, .git for version)
COPY . .

# Ensure hatch-vcs has a version to work with even without tags
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with ALL extras + dev/test deps
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
