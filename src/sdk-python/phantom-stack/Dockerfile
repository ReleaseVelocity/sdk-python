FROM python:3.12-slim

WORKDIR /app

# hatch-vcs derives the version from git tags. The source tree here has no .git,
# so provide a fallback version for the build backend.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1

# Copy dependency manifest first for better layer caching.
COPY pyproject.toml README.md ./

# Copy the full project source and install in editable/dev mode with all extras.
COPY . /app

# Install the package in editable mode with all optional + dev dependencies.
RUN pip install -e ".[all,dev]"

CMD ["sleep", "infinity"]
