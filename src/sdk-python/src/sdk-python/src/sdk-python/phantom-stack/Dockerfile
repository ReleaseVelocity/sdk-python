FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git history in this
# build context, so provide a pretend version to allow an editable install.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching. The hatch build
# backend reads README.md (referenced by pyproject) during the editable install.
COPY pyproject.toml README.md ./

# Create the package directory so the editable install can resolve the package
# layout before the full source is copied (improves cache reuse for deps).
COPY src/strands/__init__.py src/strands/__init__.py

# Install the project in editable/development mode with ALL optional features
# and the dev/test tooling so a developer can run tests, linters, type checks.
RUN pip install -e ".[all,dev]"

# Copy the rest of the source, tests, and configs.
COPY . .

CMD ["sleep", "infinity"]
