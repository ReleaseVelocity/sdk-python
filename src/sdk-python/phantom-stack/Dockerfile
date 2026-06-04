FROM python:3.12-slim

# Avoid interactive prompts and ensure unbuffered output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

# git is required because the project uses hatch-vcs (version from git tags)
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed for editable install + git metadata)
COPY . .

# hatch-vcs needs git history to determine the version. If the build context
# has no .git, provide a fallback version so the build still succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with all dev + optional deps.
# "all" pulls in every model-provider extra; "dev" pulls in test/lint tooling.
RUN python -m pip install --upgrade pip \
    && pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
