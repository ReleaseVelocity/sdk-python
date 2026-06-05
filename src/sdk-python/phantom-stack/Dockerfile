FROM python:3.12-slim

# Avoid interactive prompts and reduce noise
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs (setuptools_scm) reads the version from git tags. There is no git
# metadata in this build context, so provide a pretend version so the editable
# install can build the package metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    HATCH_VCS_PRETEND_VERSION=0.0.0+docker

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata generation).
COPY src ./src

# Install the project in editable mode with ALL optional features and dev tools.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the remainder of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
