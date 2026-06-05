FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# git is required: hatch-vcs derives the package version from git tags/metadata.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy the full project. hatch-vcs needs the whole tree (and git metadata,
# created below) to resolve the package version.
COPY . .

# hatch-vcs derives the version from git tags. The build context has no git
# metadata, so initialize a throwaway repo with a tag to satisfy versioning.
# Overridable at build time via --build-arg PKG_VERSION=...
ARG PKG_VERSION=0.1.0
RUN git init -q . \
    && git config user.email "dev@example.com" \
    && git config user.name "dev" \
    && git add -A \
    && git commit -q -m "dev build" \
    && git tag "v${PKG_VERSION}"

# Install the project in editable/development mode with ALL optional features
# plus the dev + test tooling so a developer can run tests, linters, type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["bash"]
