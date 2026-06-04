FROM python:3.12-slim

# hatch-vcs (setuptools_scm) derives the version from git tags. The source tree
# in this environment is not a git checkout, so provide a pretend version so the
# editable install can determine a version without git history.
ARG PRETEND_VERSION=1.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PRETEND_VERSION}

# Avoid interactive prompts and reduce pip noise
ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install has a target before full
# source is copied (improves cache reuse on dependency-only changes).
RUN mkdir -p src/strands

# Install build tooling, then install the project in editable/development mode
# with ALL optional features plus dev/test dependencies.
RUN pip install --upgrade pip hatchling hatch-vcs

# Now copy full source and install in editable mode with all extras + dev tools.
COPY . .

RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
