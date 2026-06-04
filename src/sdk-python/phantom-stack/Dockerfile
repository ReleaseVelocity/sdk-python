FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context may not be a
# git repo, so provide a fallback version to keep the build deterministic.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed by hatch-vcs / pre-commit; build tooling kept minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so editable metadata resolution succeeds during
# the dependency-only install layer.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install project in editable/dev mode with all optional + dev dependencies so
# tests, linters and type checkers are all available inside the container.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Now copy the full source tree.
COPY . .

# Reinstall editable to pick up real package metadata/entry points.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
