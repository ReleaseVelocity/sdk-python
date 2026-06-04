FROM python:3.12-slim

# Allow pip to install into the system environment
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# hatch-vcs derives the version from git tags. This source tree is not a git
# checkout, so provide an explicit version for the build backend instead.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source.
COPY src ./src

# Install the project in editable/development mode with all optional extras and
# the full dev/test toolchain so a developer can run tests, linters, and type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in tests and the rest of the project.
COPY tests ./tests
COPY tests_integ ./tests_integ
COPY . ./

CMD ["sleep", "infinity"]
