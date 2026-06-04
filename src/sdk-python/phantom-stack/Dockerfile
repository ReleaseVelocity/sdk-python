FROM python:3.13-slim

# hatch-vcs determines the version from git tags. Since the source is provided
# without git history, provide a pretend version so the build/install succeeds.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_NO_CACHE_DIR=1
ENV PYTHONUNBUFFERED=1

# Minimal build tooling. Most deps ship wheels; keep this lean.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install build backend).
COPY src ./src

# Install the project in editable/development mode with ALL optional
# dependencies plus the dev/test toolchain.
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs, etc.).
COPY . .

CMD ["bash"]
