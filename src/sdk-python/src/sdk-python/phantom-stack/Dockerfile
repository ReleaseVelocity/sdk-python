FROM python:3.12-slim

# hatch-vcs honors SETUPTOOLS_SCM_PRETEND_VERSION to derive a version without
# requiring real git tags (the build context has no .git history).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is the backend used by hatch-vcs; keep system deps minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifests first for better layer caching.
COPY pyproject.toml README.md ./
COPY src ./src

# Install the project in editable/development mode with all optional + dev deps.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
