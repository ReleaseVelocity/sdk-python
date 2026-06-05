# Strands Agents Python SDK - development image
# requires-python >=3.10; use slim to keep image small
FROM python:3.12-slim

# hatch-vcs derives the version from git tags. This source tree is not a git
# repo, so provide a fallback version to the build backend.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0.dev0 \
    SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0.dev0 \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1

# git is occasionally needed by build/dev tooling; keep the toolchain minimal.
RUN apt-get update \
    && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with ALL optional extras
# plus the dev/test toolchain (pytest, ruff, mypy, moto, ...).
RUN pip install --upgrade pip \
    && pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
