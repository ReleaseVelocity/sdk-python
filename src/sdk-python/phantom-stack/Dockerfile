FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree may not be a git
# repo, so provide a fallback version via setuptools_scm's pretend-version env var.
ARG SDK_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${SDK_VERSION} \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

# git is useful for hatch-vcs / pre-commit; build-essential only if a wheel needs it.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full source (needed: editable install builds the package metadata).
COPY . .

# Install the project in editable/development mode with ALL optional + dev deps.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
