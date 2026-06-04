FROM python:3.13-slim

# hatch-vcs derives the version from git tags. This source tree has no .git,
# so provide a pretend version via build arg (overridable per environment).
ARG STRANDS_VERSION=0.0.0
ENV SETUPTOOLS_SCM_PRETEND_VERSION=${STRANDS_VERSION}
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the package source (needed for editable install of the package).
COPY src ./src

# Install the project in editable/dev mode with all runtime extras plus dev/test tooling.
RUN pip install -e ".[all,dev]"

# Copy the remaining project files (tests, docs, configs).
COPY . .

CMD ["sleep", "infinity"]
