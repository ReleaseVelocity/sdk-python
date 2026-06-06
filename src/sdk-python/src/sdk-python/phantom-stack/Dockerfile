FROM python:3.13-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs determines version from git tags; this repo has no git history,
# so provide a pretend version to satisfy the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

WORKDIR /app

# Copy dependency manifest first for layer caching
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install) and tests
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/dev mode with all extras + dev/test tooling
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
