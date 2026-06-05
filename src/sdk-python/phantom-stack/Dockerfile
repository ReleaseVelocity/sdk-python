FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no git
# history, so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-install build backend so editable install resolves quickly.
RUN pip install --no-cache-dir hatchling hatch-vcs

# Copy the full source tree.
COPY . .

# Install the project in editable/dev mode with all optional + dev dependencies
# so tests, linters and type checkers are all available.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
