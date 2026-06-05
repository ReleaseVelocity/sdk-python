FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source tree may not be
# a git checkout, provide a pretend version so the editable install succeeds.
ARG PKG_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION} \
    HATCH_VCS_PRETEND_VERSION=${PKG_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for an editable install to resolve the package).
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
