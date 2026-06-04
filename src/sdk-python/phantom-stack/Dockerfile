FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git,
# so we provide a fallback version via env so the editable install succeeds.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the source package (needed because the editable install builds the
# package metadata and hatchling reads packages = ["src/strands"]).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
