FROM python:3.12-slim

# System deps: git for hatch-vcs metadata; build tools only if needed by wheels
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# hatch-vcs derives version from git tags. Source has no .git, so pin a version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+docker \
    HATCH_BUILD_NO_HOOKS=true

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy source so the editable install can resolve the package
COPY src ./src

# Install the project in editable/development mode with ALL optional deps + dev tools.
RUN pip install --upgrade pip setuptools wheel \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs)
COPY . .

CMD ["sleep", "infinity"]
