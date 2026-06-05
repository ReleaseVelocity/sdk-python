FROM python:3.12-slim

# hatch-vcs (setuptools-scm) needs a version; no git tags available in this copy.
# Provide a pretend version so the build backend can resolve the package version.
ARG PACKAGE_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION}
ENV HATCH_VCS_PRETEND_VERSION=${PACKAGE_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# System deps: git for any vcs operations, build tools only if a wheel needs compiling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        curl \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./
# Source is required because the project installs in editable/dev mode and
# hatchling references the package during build.
COPY src ./src

# Install the project in development mode with all optional + dev dependencies.
# 'all' covers every model provider + a2a + otel; 'dev' covers pytest/ruff/mypy/moto.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the full test suites and remaining project files.
COPY tests ./tests
COPY tests_integ ./tests_integ

CMD ["sleep", "infinity"]
