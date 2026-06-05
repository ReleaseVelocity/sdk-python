FROM python:3.12-slim

# Setuptools_scm / hatch-vcs needs git metadata to determine the version
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# hatch-vcs derives the version from git tags. The source tree is copied without
# .git, so provide the version explicitly to the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Copy the full source tree
COPY . /app/

# Install the project in editable/development mode with all + dev dependencies.
# (bidi extra is intentionally excluded: it requires portaudio and python>=3.12-only
#  packages, and its tests are ignored by the default pytest config.)
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
