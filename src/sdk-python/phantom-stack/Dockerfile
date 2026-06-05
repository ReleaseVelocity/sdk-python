FROM python:3.12-slim

# hatch-vcs (built on setuptools-scm) derives the version from git tags. The
# build context is not a git repo, so provide a pretend version to keep the
# editable install working.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps: git is referenced by hatch-vcs tooling; build-essential is kept
# minimal and only added if native wheels are unavailable.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git curl \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed for an editable install of the package).
COPY . .

# Install the project in editable/development mode with all optional features
# plus the dev toolchain (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
