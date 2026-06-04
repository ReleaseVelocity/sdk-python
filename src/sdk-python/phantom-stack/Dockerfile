FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# repo here, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling and the project in editable/development mode with all
# optional + dev dependencies so tests and linters are fully available.
# We pre-copy the package so the editable install can resolve the package tree,
# but keep manifests in their own layer above for caching.
COPY src ./src

RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the source (tests, configs, etc.)
COPY . .

CMD ["sleep", "infinity"]
