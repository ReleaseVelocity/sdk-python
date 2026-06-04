FROM python:3.11-slim

# hatch-vcs derives the version from git tags. Provide a fallback version so
# editable installs work even without git metadata.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION_FOR_STRANDS_AGENTS=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1
ENV PYTHONUNBUFFERED=1

# Install git (hatch-vcs build backend invokes it) and minimal build tooling.
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install resolution).
COPY src ./src

# Install the project in editable/development mode with all feature sets and
# dev/test tooling so a developer can run tests and linters.
RUN pip install --no-cache-dir -e ".[all,dev]"

# Copy the remaining project files (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
