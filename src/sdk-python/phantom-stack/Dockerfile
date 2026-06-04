FROM python:3.13-slim

# hatch-vcs (setuptools-scm) determines version from git tags. With no tags
# present, provide a pretend version via the dist-specific env var so the
# editable install can generate metadata.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# git is needed by hatch-vcs; gcc for any native wheels that lack prebuilt ones.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest + sources needed for build backend, then install.
# strands-agents uses hatchling with package src/strands, so we copy the
# project metadata and source for an editable install with all extras.
COPY pyproject.toml README.md ./
COPY src ./src

# hatch-vcs (setuptools-scm) derives the package version from git tags. The
# build context excludes .git, so create a minimal tagged repo to satisfy it.
RUN git init -q && \
    git config user.email "dev@example.com" && \
    git config user.name "dev" && \
    git add -A && \
    git commit -q -m "build" && \
    git tag v0.0.0

# Install the package in editable/development mode with all optional groups
# plus the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["bash"]
