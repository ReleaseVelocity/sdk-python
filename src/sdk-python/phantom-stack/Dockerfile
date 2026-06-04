FROM python:3.12-slim

# hatch-vcs derives the version from git tags via setuptools-scm. The source
# tree here is not a git checkout, so provide an explicit version through every
# channel the build backend may consult (the isolated pip build env reads these).
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0

# Default AWS settings so boto3/moto-based tests have a region available.
ENV AWS_DEFAULT_REGION=us-west-2 \
    PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with ALL optional features
# plus the dev/test toolchain. README.md and pyproject.toml are required by the
# hatchling build backend.
COPY src ./src
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
