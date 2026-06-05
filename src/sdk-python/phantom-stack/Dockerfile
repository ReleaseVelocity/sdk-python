FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git
# metadata, so provide a pretend version to satisfy the version source.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is referenced by hatch-vcs tooling; keep minimal build deps available.
RUN apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source tree (editable install needs the package present).
COPY . .

# Install the project in editable/development mode with all optional and dev
# dependencies so tests, linters and dev tools are available.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
