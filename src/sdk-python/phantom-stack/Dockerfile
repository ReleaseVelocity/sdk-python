FROM python:3.12-slim

# hatch-vcs derives the package version from git tags. The source tree copied
# into the image has no .git metadata, so provide an explicit pretend version
# (overridable at build time) to keep the editable install deterministic.
ARG PACKAGE_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt. git kept for tooling that
# may inspect the repo.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves before
# the full source is copied (improves cache reuse on source-only changes).
RUN mkdir -p src/strands && touch src/strands/__init__.py

# Install the project with all optional features plus dev/test dependencies so
# a developer can run tests, linters, and type checks inside the container.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Now copy the full source tree.
COPY . .

# Reinstall editable to pick up the real package layout/entrypoints.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
