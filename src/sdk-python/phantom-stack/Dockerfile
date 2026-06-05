FROM python:3.12-slim

# System deps:
# - git: hatch-vcs derives the package version from git tags/history
# - build-essential: in case any dependency needs to compile native extensions
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the rest of the source (needed for editable install + hatch-vcs version).
COPY . .

# hatch-vcs needs a git repo with at least one tag to compute a version.
# The build context may not include .git, so initialize a lightweight repo
# with a baseline tag so the editable install can resolve a version.
RUN if [ ! -d .git ]; then \
        git init -q && \
        git config user.email "dev@example.com" && \
        git config user.name "dev" && \
        git add -A && \
        git commit -qm "containerized build" && \
        git tag v0.0.0 ; \
    fi

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
