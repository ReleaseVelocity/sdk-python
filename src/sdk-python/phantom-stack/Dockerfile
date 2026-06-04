FROM python:3.12-slim

# hatch-vcs derives the version from git tags. Since the source may be shipped
# without a .git directory, provide a fallback version so the build succeeds.
ARG STRANDS_VERSION=0.0.0+local
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}

ENV PYTHONUNBUFFERED=1 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

WORKDIR /app

# git is needed by hatch-vcs / pre-commit tooling
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy source needed for an editable install.
COPY src ./src

# Install the project in editable/dev mode with all optional + dev dependencies.
# "all" mirrors the hatch-test/default env features; "dev" adds pytest, ruff,
# mypy, moto, etc. used for development and testing.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
