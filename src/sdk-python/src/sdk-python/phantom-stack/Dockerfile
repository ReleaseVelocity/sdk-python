FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to satisfy the dynamic version requirement.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install dependency sets. We pass the editable flag against a temporary
# stub so the heavy dependency resolution is cached before the full source is
# copied. We install the project itself (with [all,dev]) once the source lands.
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project sources (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
