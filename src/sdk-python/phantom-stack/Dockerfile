FROM python:3.12-slim

# hatch-vcs determines the version from git tags. Provide a fallback so the
# build does not depend on the project being a git repository.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs; build tools kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source and tests.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs a git repo with a tag to resolve the dynamic version.
# Initialize a throwaway repo and tag it so editable install succeeds even
# when the project is not under version control in the workspace.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -qm "phantom-stack build" && \
    git tag v0.0.0

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
