FROM python:3.12-slim

# Version for hatch-vcs (used when no git tags are available).
# Can be overridden at build time: --build-arg PKG_VERSION=x.y.z
ARG PKG_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=${PKG_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is required by hatch-vcs build backend (hatch.version source = "vcs").
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install build tooling.
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the full source (needed for editable install of the package).
COPY . /app

# Initialize a git repo so hatch-vcs can resolve a version, and install the
# project in editable/development mode with ALL optional features + dev tools.
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -qm "dev snapshot" \
    && git tag "v${PKG_VERSION}" \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
