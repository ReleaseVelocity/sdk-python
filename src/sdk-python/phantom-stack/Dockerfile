FROM python:3.12-slim

# Build-time metadata / version for hatch-vcs (no .git in build context)
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs / some dev tooling; build tools kept minimal
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Pre-install dependency closure (editable install of src follows after source copy)
# Install all optional feature groups + dev/test tooling.
RUN pip install --upgrade pip

# Copy full source
COPY . /app

# Editable install with all features and dev dependencies
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
