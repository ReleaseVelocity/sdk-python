FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source tree has no
# .git directory in this environment, provide a pretend version so the build
# backend (hatchling + hatch-vcs) can produce a valid version string.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage Docker layer caching.
COPY pyproject.toml README.md ./

# Copy the full source needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
