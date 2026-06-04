FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The source tree has no git
# history in this environment, so provide a pretend version to satisfy the
# build backend during the editable install.
ENV HATCH_VCS_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# git is referenced by hatch-vcs at build time; build-essential covers any
# optional C-extension builds among the many extras being installed.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Install the project in editable/development mode with all runtime extras
# plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
COPY src/ ./src/
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the rest of the source (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
