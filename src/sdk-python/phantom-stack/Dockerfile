FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Provide a fallback so the
# editable install does not fail when git metadata is unavailable.
ENV HATCH_BUILD_NO_HOOKS=false
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs to determine the version from tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the project.
COPY . .

# hatch-vcs requires a git repo with at least one tag/commit to resolve a
# version. Initialize a throwaway repo with a tag if none exists, otherwise
# the SETUPTOOLS_SCM_PRETEND_VERSION fallback (consumed by hatch-vcs) applies.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && git commit -qm "init" && \
        git tag v0.0.0; \
    fi

# Install the project in editable/development mode with ALL optional
# dependencies and the dev toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
