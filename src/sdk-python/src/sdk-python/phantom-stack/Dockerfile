FROM python:3.13-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version build step.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Source layout is required for an editable install (packages = src/strands).
COPY src ./src

# Install the project in editable/dev mode with all optional features plus
# the dev/test toolchain (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
