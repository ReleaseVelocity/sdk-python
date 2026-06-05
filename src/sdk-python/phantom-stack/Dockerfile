FROM python:3.12-slim

# Avoid interactive prompts and ensure clean Python behavior
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    PIP_NO_CACHE_DIR=1

# hatch-vcs determines version from git tags. The build context has no .git,
# so provide a pretend version for setuptools_scm (used by hatch-vcs).
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0

# System deps: git for any VCS-aware tooling, and build tools only if needed.
# portaudio is required by pyaudio (bidi extra). We install all extras for dev.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        gcc \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install metadata resolution).
COPY src ./src

# Install the project in editable/development mode with all + dev dependencies.
RUN pip install --upgrade pip && \
    pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
