FROM python:3.12-slim

# hatch-vcs derives the version from git tags; this repo has no .git in the
# container, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# The package's source must exist for an editable install to resolve packages.
COPY src ./src

# Install the project in editable/development mode with all optional features
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
