FROM python:3.13-slim

# git is required by hatch-vcs to determine the version from git tags
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs needs git metadata to compute a version. Provide a fallback
# version so the editable install succeeds even without tags.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_BUILD_HOOK_VCS_PRETEND_VERSION=0.0.0

# Copy the full source
COPY . .

# Initialize a git repo with a tag if none exists so hatch-vcs can version it,
# then install the package in editable/development mode with all extras + dev deps.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "init" && \
        git tag v0.0.0 ; \
    fi && \
    pip install -e ".[dev,all]"

CMD ["sleep", "infinity"]
