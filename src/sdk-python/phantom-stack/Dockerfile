FROM python:3.12-slim

# hatch-vcs determines version from git tags; provide a fallback so the
# editable install works without a populated git history.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# git is referenced by hatch-vcs; keep build deps minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Editable install with all optional + dev dependencies so a developer can
# run tests, linters, and any project command inside the container.
COPY src ./src
RUN pip install -e ".[all,dev]"

# Now bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
