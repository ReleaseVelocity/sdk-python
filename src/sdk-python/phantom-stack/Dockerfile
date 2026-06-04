FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context has no
# git history, provide a pretend version so the editable install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps: git is referenced by hatch-vcs; build-essential covers any
# packages that need native compilation.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        curl \
    && rm -rf /var/lib/apt/lists/*

# Upgrade pip tooling first.
RUN pip install --no-cache-dir --upgrade pip setuptools wheel

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the
# package and for hatchling to find packages = ["src/strands"]).
COPY src/strands ./src/strands

# Install the project in editable/development mode with ALL optional
# dependencies and the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
