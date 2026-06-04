FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the build context may not
# contain a populated .git directory, provide a pretend version so the build
# backend can resolve a version without failing.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0+dev
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# Minimal system deps. git is useful for hatch-vcs / pre-commit workflows.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Upgrade pip tooling first (cached layer).
RUN python -m pip install --upgrade pip setuptools wheel

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src/ ./src/

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN python -m pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
