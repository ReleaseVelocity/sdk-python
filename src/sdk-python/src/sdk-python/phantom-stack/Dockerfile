FROM python:3.12-slim

# git is needed for hatch-vcs version detection and pre-commit hooks
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git tags. With no .git present (or to make
# the build deterministic), provide a pretend version so the editable install
# and test collection succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Initialize a git repo with a tag so hatch-vcs can resolve a version even if
# HATCH_VCS_PRETEND_VERSION is not honored by a given hatch-vcs version.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && git commit -qm "init" && \
    git tag v0.0.0

# Copy the actual source package and tests
COPY src/strands ./src/strands
COPY tests ./tests
COPY tests_integ ./tests_integ

# Re-commit/tag so the working tree is clean for hatch-vcs
RUN git add -A && git commit -qm "src" --allow-empty && git tag -f v0.0.0

# Install the project in editable/development mode with ALL extras + dev tools.
# "all" pulls every model provider + a2a + otel; "dev" pulls pytest/ruff/mypy/moto/etc.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
