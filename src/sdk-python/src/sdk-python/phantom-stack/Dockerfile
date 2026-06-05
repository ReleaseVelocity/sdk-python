FROM python:3.12-slim

# Avoid interactive prompts and ensure clean Python output
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# System deps:
# - git: hatch-vcs derives the version from git tags/history
# - build-essential: in case any dependency needs to compile native extensions
WORKDIR /app

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# hatch-vcs requires a git repo with at least one tag to compute a version.
# Initialize a throwaway repo and tag it so the editable install succeeds even
# when build context lacks .git history.
RUN pip install --upgrade pip && \
    git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git -c commit.gpgsign=false commit -qm "init" && \
    git tag v0.0.0

# Copy the source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with ALL extras plus dev/test tooling.
# 'all' pulls in every model provider + a2a + otel + docs.
# 'dev' pulls in pytest, ruff, mypy, moto, etc.
RUN pip install -e ".[all,dev]"

# Bring in the test suites so a developer can run them inside the container.
COPY tests ./tests
COPY tests_integ ./tests_integ
COPY .codecov.yml ./

# Re-stage files so the working tree is clean for tools that inspect git.
RUN git add -A && git -c commit.gpgsign=false commit -qm "sources" || true

CMD ["sleep", "infinity"]
