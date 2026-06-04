FROM python:3.12-slim

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    # hatch-vcs (setuptools-scm based) needs a version; no .git in build context
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

WORKDIR /app

# System deps: git for hatch-vcs fallback, build tools occasionally needed by deps
RUN apt-get update \
    && apt-get install -y --no-install-recommends git curl \
    && rm -rf /var/lib/apt/lists/*

# Leverage layer caching: copy manifest first
COPY pyproject.toml README.md ./

# Copy source needed for editable install (package metadata reads src/strands)
COPY src ./src

# Install the project in editable/development mode with ALL optional deps + dev/test deps
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
