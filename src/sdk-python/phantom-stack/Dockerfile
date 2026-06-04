FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree here has no
# .git directory, so provide a pretend version to satisfy the build backend.
ARG PKG_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PKG_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata).
COPY src ./src

# Install the project in editable/development mode with all optional
# dependencies plus the dev/test tooling (pytest, moto, ruff, mypy, ...).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
