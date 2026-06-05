# Strands Agents Python SDK - development image
# Python >=3.10 required; 3.12 chosen to satisfy all optional deps (incl. bidi gates).
FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required at install time because the build backend (hatch-vcs) derives
# the package version from git tags. Minimal toolchain otherwise.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        curl \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The source tree is bind-mounted at runtime by docker-compose, but we also copy
# it so the image is self-contained and the editable install can resolve packages.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# hatch-vcs needs a git repo with a tag to compute the version. The real project
# is checked out without .git in this environment, so we seed a minimal repo and
# tag so the editable install succeeds with a deterministic version.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "containerize snapshot" \
    && git tag v0.0.0

# Install the project in editable/development mode with ALL optional features and
# the full dev toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
