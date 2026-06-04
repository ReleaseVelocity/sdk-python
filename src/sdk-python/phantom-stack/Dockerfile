FROM python:3.12-slim

# hatch-vcs (setuptools_scm) needs a version; there's no .git in the build
# context, so provide a fallback pretend version.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Build tooling for any native deps (kept minimal).
RUN apt-get update && apt-get install -y --no-install-recommends \
        gcc \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of the package itself).
COPY src ./src

# Install the project in editable/development mode with dev + all optional deps.
RUN pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
