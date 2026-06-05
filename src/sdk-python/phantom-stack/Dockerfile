# Strands Agents Python SDK - development image
# Requires Python >=3.10; use 3.12 so optional bidi deps stay installable if needed.
FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree is not a git
# checkout here, so provide a pretend version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with all optional features
# plus dev/test tooling (pytest, ruff, mypy, moto, etc.).
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
