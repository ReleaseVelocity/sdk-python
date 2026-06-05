FROM python:3.12-slim

# hatch-vcs derives the version from git tags via setuptools-scm. The build
# context has no .git, so provide a pretend version to satisfy the backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install build tooling needed for editable install / version resolution.
RUN pip install --upgrade pip hatchling hatch-vcs

# Copy the rest of the source.
COPY . /app

# Install the project in editable/development mode with ALL extras + dev/test deps.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
