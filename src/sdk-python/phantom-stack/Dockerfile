FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide an explicit version override (read by setuptools-scm/hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling.
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the full project source.
COPY . /app

# Install the project in editable/development mode with ALL runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
