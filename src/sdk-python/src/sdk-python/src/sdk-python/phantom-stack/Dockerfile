FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git history in the
# build context, so provide a pretend version for setuptools_scm/hatch-vcs.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION} \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION} \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is kept available for general development use inside the container.
# build-essential covers any source builds for transitive dependencies.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source and tests required for an editable install + testing.
COPY src ./src
COPY tests ./tests
COPY tests_integ ./tests_integ

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain.
RUN pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
