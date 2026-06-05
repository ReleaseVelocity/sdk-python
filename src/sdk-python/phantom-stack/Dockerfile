FROM python:3.12-slim

# hatch-vcs derives the version from git tags; this repo ships without a .git
# directory, so provide a pretend version (setuptools_scm honours this var).
ARG PRETEND_VERSION=1.0.0.dev0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for the editable install to resolve the
# package, and so optional/dev extras can be installed in one shot).
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL optional providers
# plus the full dev/test toolchain (pytest, moto, ruff, mypy, ...).
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
