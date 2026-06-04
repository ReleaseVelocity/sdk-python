FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no .git
# directory in this environment, so provide a pretend version to allow the
# editable install / build backend to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The package directory must exist for an editable install to resolve.
COPY src ./src

# Install the project in editable/development mode with ALL optional feature
# groups plus the dev/test tooling (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Copy the remaining project sources (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
