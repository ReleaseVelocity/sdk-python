FROM python:3.12-slim

# hatch-vcs derives the version from git tags. There is no git history in this
# build context, so provide a pretend version for setuptools-scm/hatch-vcs.
ARG PROJECT_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${PROJECT_VERSION}

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt but some deps may compile.
RUN apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        curl \
    && rm -rf /var/lib/apt/lists/*

RUN pip install --upgrade pip

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install in editable/development mode with the full dependency set
# (all model providers + dev/test tooling) so tests and linters work.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
