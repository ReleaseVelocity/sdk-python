FROM python:3.13-slim

# git is required because hatch-vcs derives the version from git tags/metadata
RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Copy dependency manifest first for better layer caching
COPY pyproject.toml README.md ./

# Copy the full project (source, tests, git metadata)
COPY . .

# hatch-vcs needs version info. If git metadata is missing, provide a fallback
# so the build doesn't fail when the source is copied without .git.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV HATCH_VCS_PRETEND_VERSION=0.0.0

# Install the project in editable/development mode with all runtime extras
# plus the dev/test tooling (pytest, ruff, mypy, moto, etc.)
RUN pip install --upgrade pip \
    && pip install --no-cache-dir -e ".[all,dev]"

CMD ["sleep", "infinity"]
