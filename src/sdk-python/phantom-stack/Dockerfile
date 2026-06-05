FROM python:3.12-slim

# hatch-vcs derives the version from git tags; the workspace is not a git repo,
# so provide a pretend version to allow an editable install to succeed.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so an editable install can resolve packages.
COPY src ./src

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install -e ".[all,dev]"

# Bring in the rest of the source (tests, configs).
COPY . .

CMD ["sleep", "infinity"]
