FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to setuptools_scm (used by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install).
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling so a developer can run tests and linters.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
