FROM python:3.12-slim

# System deps: git is needed by hatch-vcs to determine version from tags.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# hatch-vcs derives the version from git metadata. The build context may not
# include a usable .git, so provide a fallback version via env so the editable
# install does not fail.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Pre-create the package dir so the editable install metadata resolves before
# the full source copy (kept minimal; full source copied below).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus dev/test tooling so a developer can run tests and linters.
# hatch-vcs needs git tags; if absent, hatch falls back to the env var below.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
RUN pip install --upgrade pip setuptools wheel hatchling hatch-vcs \
    && pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, etc.).
COPY . .

CMD ["sleep", "infinity"]
