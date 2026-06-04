FROM python:3.12-slim

# Version that hatch-vcs (setuptools_scm) will use when git metadata is unavailable.
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=${STRANDS_VERSION}

# git is required by hatch-vcs to resolve the package version.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

RUN pip install --no-cache-dir --upgrade pip

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Copy the package source so the editable install can resolve the package.
COPY src ./src

# Install the project in editable/development mode with all optional providers
# (anthropic, gemini, litellm, openai, etc.) plus the dev/test toolchain
# (pytest, pytest-asyncio, pytest-xdist, moto, mypy, ruff, ...).
RUN pip install --no-cache-dir -e ".[dev,all]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
