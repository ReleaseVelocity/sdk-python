FROM python:3.13-slim

# hatch-vcs derives the version from git tags. Since the source tree may not
# include a .git directory, provide a fallback version so the build succeeds.
ARG PACKAGE_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PACKAGE_VERSION}
ENV PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is needed by some tooling/pre-commit; build tools kept minimal.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (required for editable install resolution).
COPY src ./src

# Install the project in editable/development mode with all dev + extra deps.
RUN pip install --upgrade pip \
    && pip install -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
