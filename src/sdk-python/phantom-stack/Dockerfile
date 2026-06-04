FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version; no git repo is present in the source tree.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is referenced by hatch-vcs; keep it available for dev workflows.
RUN apt-get update && apt-get install -y --no-install-recommends git && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed for editable install of the package).
COPY . .

# Install the project in editable/development mode with all optional + dev/test deps.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
