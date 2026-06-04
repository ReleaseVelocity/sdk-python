FROM python:3.12-slim

# hatch-vcs derives the version from git tags. The build context has no .git
# directory, so provide an explicit version to the setuptools_scm backend that
# hatch-vcs relies on.
ENV SETUPTOOLS_SCM_PRETEND_VERSION=0.0.0
ENV PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_NO_CACHE_DIR=1 \
    PYTHONUNBUFFERED=1

WORKDIR /app

# Copy dependency manifest first to leverage layer caching.
COPY pyproject.toml README.md ./

# Create the package source dir so the editable install metadata resolves.
COPY src ./src

# Install the project in editable/development mode with all optional
# dependencies plus the dev/test toolchain (pytest, moto, ruff, mypy, etc.).
RUN pip install -e ".[all,dev]"

# Bring in the rest of the project (tests, configs, docs).
COPY . .

CMD ["sleep", "infinity"]
