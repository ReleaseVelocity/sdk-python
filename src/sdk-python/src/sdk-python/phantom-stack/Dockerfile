FROM python:3.12-slim

# Install git (needed by hatch-vcs to determine version from git tags) and
# build tooling for any native deps.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full project (source, tests, git metadata) so editable install +
# hatch-vcs versioning works.
COPY . .

# hatch-vcs needs git metadata to compute a version. If the project was copied
# without a usable git history, provide a fallback version so the build does
# not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with all runtime extras and
# the dev/test toolchain so developers can run tests, linters, and type checks.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
