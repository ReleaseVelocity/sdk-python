FROM python:3.12-slim

# Hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version that setuptools_scm/hatch-vcs will honor.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# The package source must exist for an editable install to resolve.
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
