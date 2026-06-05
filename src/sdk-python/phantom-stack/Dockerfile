FROM python:3.12-slim

# hatch-vcs derives the package version from git metadata. The build context
# has no .git directory, so initialize a throwaway repo with a tag inside the
# image. This lets setuptools-scm/hatch-vcs resolve a concrete version.
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# The package uses a src-layout; create the package dir so an editable install
# can resolve metadata during the dependency-only pre-install step.
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Minimal git repo + tag so hatch-vcs detects version 0.0.0.
RUN git init -q && git config user.email dev@example.com && git config user.name dev \
    && git add -A && git commit -qm init && git tag v0.0.0

# Install all runtime + optional + dev/test dependencies in editable mode.
# "all" pulls every model-provider extra; "dev" adds pytest, ruff, mypy, moto, etc.
RUN pip install -e ".[all,dev]"

# Now copy the full source tree (overwrites the placeholder package files).
COPY . .

# Re-create git metadata (COPY . . may have brought a .git or none) and reinstall
# so editable metadata reflects the real source. Deps are cached so this is fast.
RUN rm -rf .git && git init -q && git config user.email dev@example.com \
    && git config user.name dev && git add -A && git commit -qm init && git tag v0.0.0 \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
