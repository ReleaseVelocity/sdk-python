FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree has no .git,
# so we provide a fallback version that hatch-vcs / setuptools-scm honors.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0+dev \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1

# git is needed by hatch-vcs build backend; build tools for any native wheels.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed because install is editable and resolves the package).
COPY src ./src

# Install the project in editable/development mode with ALL optional features
# plus the dev tooling (pytest, ruff, mypy, moto, hatch, etc.).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]" \
    && pip install hatch uv

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
