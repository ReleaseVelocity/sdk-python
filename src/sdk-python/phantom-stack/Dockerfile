FROM python:3.13-slim

# Allow overriding the SDK version (project uses hatch-vcs / git tags for versioning).
ARG STRANDS_VERSION=0.0.0

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs is built on setuptools_scm; this lets the build resolve a version
    # without requiring a populated git history at build time.
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION}

# git is needed because the build backend (hatch-vcs) inspects version control.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Initialize a minimal git repo with a tag so hatch-vcs can resolve a version
# during the editable install (the source tree ships without a .git directory).
RUN git init -q \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "init" \
    && git tag "v${STRANDS_VERSION}"

# Bring in the rest of the source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Add the remaining project files (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
