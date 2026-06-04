FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git,
# so provide a pretend version to allow editable install to succeed.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Minimal build tooling; most wheels are prebuilt for slim.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source needed for an editable install.
COPY src ./src

# Install the project in editable/development mode with all optional
# feature deps plus the dev/test toolchain.
RUN pip install -e ".[all,dev]"

# Copy the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
