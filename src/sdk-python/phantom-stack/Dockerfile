FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no .git in this
# build context, so provide a pretend version to satisfy the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package source so the editable build backend can resolve it,
# then install all runtime + optional + dev dependencies.
COPY src ./src

# Install the project in editable/development mode with all optional features
# and the dev tooling (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
