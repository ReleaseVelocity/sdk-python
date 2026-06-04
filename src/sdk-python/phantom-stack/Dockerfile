FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Minimal system deps. git is needed because hatch-vcs derives the
# package version from git tags at build/install time.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install + version detection).
COPY src ./src

# hatch-vcs needs a git repo with a tag to compute the version.
# The real source has no .git, so create a throwaway repo with a tag.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "containerized build" \
    && git tag v1.0.0

# Install the project in editable/development mode with ALL optional
# dependencies and the dev/test toolchain (pytest, ruff, mypy, moto, etc).
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

# Re-init git after full copy so tooling/version still resolves, and so the
# editable install metadata remains valid.
RUN rm -rf .git \
    && git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "containerized build" \
    && git tag v1.0.0

CMD ["sleep", "infinity"]
